# Enabling or disabling tracing

It is possible to use `/sys/kernel/debug/tracing/tracing_on` to enable or disable tracing for debugging purpose.

```sh
# Enabling tracing
$ echo 1 > /sys/kernel/debug/tracing/tracing_on

# Disabling tracing
$ echo 0 > /sys/kernel/debug/tracing/tracing_on
```

## Resources

- [ftrace - Function Tracer](https://docs.kernel.org/trace/ftrace.html)
