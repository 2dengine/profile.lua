# Profile.lua
profile.lua is a small module used for finding bottlenecks in your Lua code.
To use the profiler, you need to require the profile.lua file and specify when to start or stop collecting data.
Once you are done profiling, a report is generated, describing:
* which functions were called most frequently and
* how much time was spent executing each function

The source code is available on [GitHub](https://github.com/2dengine/profile.lua) and the official documentation is hosted on [2dengine.com](https://2dengine.com/doc/profile.html)

# Compatibility
LuaJIT optimizations need to be disabled when using the profiler and co-routines are not supported.
The profiler has been tested with both LuaJIT 2.0.5 and Lua 5.3 although there are no guarantees regarding its accuracy.
Use at your own discretion!

# Examples
## Pure Lua
~~~~
local profile = require("profile")
profile.start()
-- execute code that will be profiled
profile.stop()
-- report for the top 10 functions, sorted by execution time
print(profile.report(10))
~~~~

## LÖVE
~~~~
-- setup
function love.load()
  love.profiler = require('profile') 
  love.profiler.start()
end

-- generates a report every 100 frames
love.frame = 0
function love.update(dt)
  love.frame = love.frame + 1
  if love.frame%100 == 0 then
    love.report = love.profiler.report(20)
    love.profiler.reset()
  end
end

-- prints the report
function love.draw()
  love.graphics.print(love.report or "Please wait...")
end
~~~~

# Reports
The default report is in plain text:
~~~~
 +-----+----------------------------------+----------+--------------------------+----------------------------------+
 | #   | Function                         | Calls    | Time                     | Code                             |
 +-----+----------------------------------+----------+--------------------------+----------------------------------+
 | 1   | update                           | 1        | 9.0023296745494          | main.lua:23                      |
 | 2   | f                                | 1        | 9.0022503120126          | main.lua:12                      |
 | 3   | g                                | 8        | 8.0016986143455          | main.lua:5                       |
 | 4   | [string "boot.lua"]:185          | 3        | 2.4960798327811e-005     | [string "boot.lua"]:185          |
 | 5   | [string "boot.lua"]:134          | 2        | 1.7920567188412e-005     | [string "boot.lua"]:134          |
 | 6   | [string "boot.lua"]:188          | 1        | 1.6000514733605e-005     | [string "boot.lua"]:188          |
 | 7   | [string "boot.lua"]:182          | 1        | 1.2160395272076e-005     | [string "boot.lua"]:182          |
 | 8   | [string "boot.lua"]:131          | 1        | 1.0240328265354e-005     | [string "boot.lua"]:131          |
 | 9   | load                             | 0        | 0                        | main.lua:17                      |
 +-----+----------------------------------+----------+--------------------------+----------------------------------+
~~~~

The "Function" column shows the function name. Closures that do not have a defined name will be replaced with the source code file and line number.
The "Calls" column tracks the total number of function calls.
The "Time" column records the total elapsed time. This value is usually in seconds.
The "Code" column shows the source file and the line number. Chunks executed using loadstring are labeled as "string".
It's easy to generate any type of report that you want, for example CSV:

~~~~
print('Position,Function name,Number of calls,Time,Source,')
for t in ipairs(profiler.query(10)) do
  print(table.concat(t, ",")..",")
end
~~~~

# Credits
0x25a0
grump
Roland Yonaba

Please support our work so we can release more free software in the future.