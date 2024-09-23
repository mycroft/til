# Creating a basic eBPF app in C

This is a basic eBPF program binding `xdp` on your preferred interface (default is `lo`), and will print some trace in `/sys/kernel/debug/tracing/trace_pipe` for each packet received.

## Kernel

The eBPF program (ie: kernel part) is looking as the following:

```sh
#include <stddef.h>

#include <linux/bpf.h>
#include <linux/in.h>
#include <linux/if_ether.h>
#include <linux/ip.h>

#include <bpf/bpf_helpers.h>
#include <bpf/bpf_endian.h>

SEC("xdp")
int basic(struct xdp_md *ctx)
{
    bpf_printk("hello xdp");

    return XDP_PASS;
}

char _license[] SEC("license") = "GPL";
```

## Userland

```sh
#include <stdio.h>
#include <unistd.h>
#include <net/if.h>

#include "basic_kern.skel.h"

int main(int argc, char **argv)
{
    char *interface_name = "lo";
    struct basic_kern *skel;
    int ret = 0;

    if (argc > 1) {
        interface_name = argv[argc - 1];
    }

    unsigned int ifindex = if_nametoindex(interface_name);
    
    if (ifindex == 0) {
        fprintf(stderr, "ERROR: failed to retrieve interface index of %s", interface_name);
        return 1;
    }

    skel = basic_kern__open();
    if (!skel) {
        fprintf(stderr, "ERROR: failed to open BPF skeleton\n");
        return 1;
    }

    ret = basic_kern__load(skel);
    if (ret) {
        fprintf(stderr, "Failed to load BPF skeleton\n");
        goto cleanup;
    }

    bpf_program__attach_xdp(skel->progs.basic, ifindex);

    // ... read the trace output, sleep, do whatever is needed to avoid program to quit

cleanup:
    basic_kern__destroy(skel);
    return ret;
}
```

Note: the `basic_kern.skel.h` file is generated with `bpftool`:

```
$ bpftool gen skeleton basic_kern.o > basic_kern.skel.
```

## Makefile

```makefile
TARGET=basic

USER_TARGET = ${TARGET:=_user}
BPF_TARGET = ${TARGET:=_kern}
BPF_C = ${BPF_TARGET:=.c}
BPF_OBJ = ${BPF_C:.c=.o}

all: clean kern $(USER_TARGET)

kern: $(BPF_OBJ)
	llvm-strip -g $(BPF_OBJ)
	bpftool gen skeleton $(BPF_OBJ) > $(BPF_TARGET).skel.h

$(USER_TARGET): %: %.c  
	gcc -Wall $(CFLAGS) -Ilibbpf/src -Ilibbpf/src/include/uapi -Llibbpf/src -o $@ $< -lbpf -lelf -lz

$(BPF_OBJ): %.o: %.c
	clang -g -O2 -target bpf -c $< -o $@

clean:
	rm -f $(BPF_OBJ) $(USER_TARGET) $(BPF_TARGET).skel.h

run: $(USER_TARGET) kern
	sudo ./$(USER_TARGET)

.PHONY: all kern clean run
```

## Building and running:

```sh
$ ls
basic_kern.c  basic_user.c  Makefile

$ make
rm -f basic_kern.o basic_user basic_kern.skel.h
clang -g -O2 -target bpf -c basic_kern.c -o basic_kern.o
llvm-strip -g basic_kern.o
bpftool gen skeleton basic_kern.o > basic_kern.skel.h
gcc -Wall  -Ilibbpf/src -Ilibbpf/src/include/uapi -Llibbpf/src -o basic_user basic_user.c -lbpf -lelf -lz

$ sudo ./basic_user lo
...
```

In another terminal:

```sh
$ ping -c 1 localhost
PING localhost (::1) 56 data bytes
64 bytes from localhost (::1): icmp_seq=1 ttl=64 time=0.178 ms

--- localhost ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.178/0.178/0.178/0.000 ms
```

... this will print in a console tailing the `tracing_pipe` file:

```
           <...>-22854   [020] ..s21  4323.273485: bpf_trace_printk: hello xdp
           <...>-22854   [020] ..s21  4323.273547: bpf_trace_printk: hello xdp
 DNS Res~ver #37-16290   [004] ..s21  4327.952397: bpf_trace_printk: hello xdp
           <...>-1679    [003] ..s21  4327.984450: bpf_trace_printk: hello xdp
 DNS Res~ver #30-8354    [014] ..s21  4331.159694: bpf_trace_printk: hello xdp
 systemd-resolve-1679    [003] ..s21  4331.160336: bpf_trace_printk: hello xdp
 DNS Res~ver #36-16289   [013] ..s21  4348.184675: bpf_trace_printk: hello xdp
 systemd-resolve-1679    [003] ..s21  4348.194696: bpf_trace_printk: hello xdp
           <...>-7522    [019] ..s21  4349.562855: bpf_trace_printk: hello xdp
 systemd-resolve-1679    [003] ..s21  4349.563329: bpf_trace_printk: hello xdp
```

## Misc

```sh
$ sudo bpftool prog
107: xdp  name basic  tag 0382e177d377c3ff  gpl
	loaded_at 2024-09-23T17:06:59+0200  uid 0
	xlated 48B  jited 47B  memlock 4096B  map_ids 25
	btf_id 242
	pids basic_user(23779)
...

