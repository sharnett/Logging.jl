Logging.jl: Basic logging for Julia
===================================

This module provides basic logging facilities for Julia.  It was inspired somewhat by logging in Python.

Install with `Pkg.add("Logging")` at the Julia prompt.

Usage
-----

If `log_test.jl` contains

```julia
using Logging

function log_test()
    debug("debug message")
    info("info message")
    warn("warning message")
    err("error message")
    critical("critical message")
end

println("Setting level=DEBUG")
Logging.configure(level=DEBUG)
log_test()

println()
println("Setting level=INFO")
Logging.configure(level=INFO)
log_test()

println()
println("Setting level=WARNING")
Logging.configure(level=WARNING)
log_test()

println()
println("Setting level=ERROR")
Logging.configure(level=ERROR)
log_test()

println()
println("Setting level=CRITICAL")
Logging.configure(level=CRITICAL)
log_test()
```

Running this gives 

```
julia> include("log_test.jl")
Setting level=DEBUG
30-Oct 22:09:20:DEBUG:root:debug message
30-Oct 22:09:20:INFO:root:info message
30-Oct 22:09:20:WARNING:root:warning message
30-Oct 22:09:20:ERROR:root:error message
30-Oct 22:09:20:CRITICAL:root:critical message

Setting level=INFO
30-Oct 22:09:20:INFO:root:info message
30-Oct 22:09:20:WARNING:root:warning message
30-Oct 22:09:20:ERROR:root:error message
30-Oct 22:09:20:CRITICAL:root:critical message

Setting level=WARNING
30-Oct 22:09:20:WARNING:root:warning message
30-Oct 22:09:20:ERROR:root:error message
30-Oct 22:09:20:CRITICAL:root:critical message

Setting level=ERROR
30-Oct 22:09:20:ERROR:root:error message
30-Oct 22:09:20:CRITICAL:root:critical message

Setting level=CRITICAL
30-Oct 22:09:20:CRITICAL:root:critical message
```

At the Julia prompt, the messages will display in color (debug=cyan,
info=blue, warning=purple, error=red, critical=red).

Logging Macros
--------------

For the functions above, there is always a small overhead for the
function call even when there is no log output. Logging.jl provides
macros which work like the functions above, but which remove this
overhead.

For example, to turn on debugging during development:

```julia
using Logging
Logging.configure(level=DEBUG)

function macro_log_test()
    @debug("debug message")
    @info("info message")
    @warn("warning message")
    @err("error message")
    @critical("critical message")
end

macro_log_test()
```

This gives:

```julia
30-Oct 22:28:51:DEBUG:root:debug message
30-Oct 22:28:51:INFO:root:info message
30-Oct 22:28:51:WARNING:root:warning message
30-Oct 22:28:51:ERROR:root:error message
30-Oct 22:28:51:CRITICAL:root:critical message
```

Later, we may decide to turn off logging entirely:

```julia
using Logging
Logging.configure(level=OFF)

function macro_log_test()
    @debug("debug message")
    @info("info message")
    @warn("warning message")
    @err("error message")
    @critical("critical message")
end

macro_log_test()
```

This prevents any of the logging code from being generated.

Note that changing the log level later in the code will not have any
affect on previously evaluated functions, though it does affect future
evaluation:


```julia
using Logging
Logging.configure(level=OFF)

function macro_log_test()
    @debug("debug message")
    @info("info message")
    @warn("warning message")
    @err("error message")
    @critical("critical message")
end

macro_log_test()

println("Setting level=DEBUG")
Logging.configure(level=DEBUG)
macro_log_test()

@warn("This warning message will print")
@debug("So will this debug message!")
```

produces:

```julia
Setting level=OFF

Setting level=DEBUG
30-Oct 23:26:16:WARNING:root:This warning message will print
30-Oct 23:26:16:DEBUG:root:So will this debug message!
```
