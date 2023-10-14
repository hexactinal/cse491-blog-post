# Raylib with Emscripten exploration
## Author: Jayson Van Dam

### Overview
My goal with this blog post is to get Raylib's
[Arkanoid example](https://github.com/raysan5/raylib-games/blob/master/classics/src/arkanoid.c)
working in a web browser using the Emscripten compiler to compile C++ source code
into WebAssembly. I will be following [this guide](https://github.com/raysan5/raylib/wiki/Working-for-Web-%28HTML5%29)
to install both Emscripten and Raylib on Windows.

### Emscripten Windows installation steps
1. First, be sure that [Python](https://www.python.org/)
and [Git](https://git-scm.com/) are both installed on the system.
1. Download the Emscripten SDK as a zip file from https://github.com/emscripten-core/emsdk/archive/refs/heads/main.zip
1. Open the zip file (with 7-zip or another archiving tool),
then go into the `emsdk-main` directory.
Extract the contents of this directory to `C:\emsdk\`
1. Open Command Prompt, then run `cd C:\emsdk\`
1. Run `emsdk.bat install latest`
1. Run `emsdk.bat activate latest`
1. Run `emsdk_env.bat`
1. You should now be able to run `emcc --version`. It should give output similar to this:
    ```
    emcc (Emscripten gcc/clang-like replacement + linker emulating GNU ld) 3.1.47 (431685f05c67f0424c11473cc16798b9587bb536)
    Copyright (C) 2014 the Emscripten authors (see AUTHORS.txt)
    This is free and open source software under the MIT license.
    There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    ```
1. From now on, if you want to compile with Emscripten,
then go to `C:\emsdk` in File Explorer and run `emcmdprompt.bat`.

### Difficulty of Raylib installation
Raylib seems a bit more challenging to install.
At first, I tried to run `pacman -S mingw-w64-x86_64-raylib`
(as suggested in the [Working on Windows](https://github.com/raysan5/raylib/wiki/Working-on-Windows)
wiki page), but this did not seem to work at first.

Second, I tried downloading the Raylib installer at
https://github.com/raysan5/raylib/releases/download/4.5.0/raylib_installer_v4.5.mingw.64bit.exe.
This seemed to work better and installed Raylib at `C:\raylib`.

Then, I read a bit deeper and found that the instructions say that:
    "Before compiling your game, raylib library **must be recompiled for HTML5**,
    generating `libraylib.a`."

I got a few compilation errors before when trying to compile a game,
so this makes much more sense. I used the instructions for "2.2 Using Makefile",
which seemed to recompile raylib correctly.

When compiling the Arkanoid example game with `mingw32-make PLATFORM=PLATFORM_WEB -B -e`,
it failed at first with this error message:
    ```
    C:/msys64/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/13.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: cannot find ../../../raylib/src/libraylib.a: No such file or directory
    ```

I thought that changing the raylib path would be much easier when compiling on the command line.
I ran `emcc -o arkanoid.html arkanoid.cpp -Os -Wall C:\raylib\raylib\src\libraylib.a -I. -IC:\raylib\raylib\src -L. -LC:\raylib\raylib\src -s USE_GLFW=3 --shell-file C:\raylib\raylib\src\shell.html -DPLATFORM_WEB`
and got this error message:
    ```
    arkanoid.cpp:119:28: error: non-constant-expression cannot be narrowed from type 'int' to 'float' in initializer list
      [-Wc++11-narrowing]
        119 |     brickSize = (Vector2){ GetScreenWidth()/BRICKS_PER_LINE, 40 };
    ```

For assistance with this error, I looked to the
[Using raylib with Cpp page](https://github.com/raysan5/raylib/wiki/Using-raylib-with-Cpp).
Since I changed the `arkanoid.c` example to `arkanoid.cpp`,
I need to replace any instances of structure initialization with brace initialization.
For these, I usually just needed to remove the parentheses around the type,
so this C code from line 119: <br />
    ```
    brickSize = (Vector2){ GetScreenWidth()/BRICKS_PER_LINE, 40 };
    ``` <br />
would be converted into this C++ code: <br />
    ```
    brickSize = Vector2{ static_cast<float>(GetScreenWidth()/BRICKS_PER_LINE), 40 };
    ``` <br />

How to compile the Arkanoid example:
1. Open File Explorer, launch `C:\emsdk\emcmdprompt.bat`
2. Change directory into `\path\to\source\`
3. Run this command:
    ```
    emcc -o arkanoid.html arkanoid.cpp -Os -Wall C:\raylib\raylib\src\libraylib.a -I. -IC:\raylib\raylib\src -L. -LC:\raylib\raylib\src -s USE_GLFW=3 --shell-file C:\raylib\raylib\src\shell.html -DPLATFORM_WEB
    ```

How to test the Arkanoid example:
1. Launch the web server with `python -m http.server 8080`
2. Visit `http://localhost:8080` in a web browser
<!-- Note any changes I had to make to the source code -->
<!-- Will probably have to make the Arkanoid example a cpp file instead of a c file. -->

### Experience getting functions and classes working
1. Add experience

### Difficulty of integration and making changes
1. Add difficulty

### When I would recommend using Raylib
1. add when I would recommend

### Tips for new Raylib users
1. add tips

### Conclusion
add conclusion