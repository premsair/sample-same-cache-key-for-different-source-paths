This sample demonstrates that with Gradle core cpp-library plugin (v7.6.4), the cache key calculated is same for the sources moved around under src/main/cpp

# Use-Case where the cache key is calculated the same 
- in this project, `a.cpp`, `b.cpp`, `a.h`, `b.h` are under `src/main/cpp` initially
- `a.cpp` includes `a.h` & `b.cpp` includes both `a.h` and `b.h`
- when project is built with the command `gradlew assembleRelease -Dorg.gradle.caching=true -Dorg.gradle.caching.debug=true -Dorg.gradle.console=verbose > before-move.log`, the cache key calculated for the `:lib:compileReleaseCpp` task is `a25ec34f8127163666403aa97a26ada8`
- now move the `a.cpp` to `src/main/cpp/subdir`
- build the project with the command `gradlew assembleRelease -Dorg.gradle.caching=true -Dorg.gradle.caching.debug=true -Dorg.gradle.console=verbose > after-move.log`, the cache key calcuated for the `:lib:compileReleaseCpp` task is `a25ec34f8127163666403aa97a26ada8` which is same as before and the `Task :lib:compileReleaseCpp - UP-TO-DATE`
- re-run the build with `--rerun-tasks` flag added to force the compile task to run, now it fails with the error `lib/src/main/cpp/subdir/a.cpp:1:10: fatal error: a.h: No such file or directory`
- since `a.cpp` is including header file `a.h` and during the move the header file `a.h` is not moved into the new `src/main/cpp/subdir` then it leads to a false cache hit after the move where the build should have failed in the second build itself