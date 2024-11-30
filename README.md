# `configure-genrule-rootdirs-repro`

## Repro
- Run `bazel configure` and abserve an error like this:
  ```
  Updating BUILD files for javascript
  Failed to validate dependencies for target "//pkgs/foo-lib:typescript":
  
  Import "./r1" from "pkgs/foo-lib/src/index.ts" is an unknown dependency. Possible solutions:
  	1. Instruct Gazelle to resolve to a known dependency using a directive:
  		# aspect:resolve [src-lang] js import-string label
  		   or
  		# aspect:js_resolve import-string-glob label
  	2. Ignore the dependency using the '# aspect:js_ignore_imports ./r1' directive.
  	3. Disable Gazelle resolution validation using '# aspect:js_validate_import_statements off'
  ```
- Manually add the generaed file to the `ts_project()` in `//pkgs/foo-lib`:
  ```diff
  diff --git a/pkgs/foo-lib/BUILD.bazel b/pkgs/foo-lib/BUILD.bazel
  index 93bfafd..4a82486 100644
  --- a/pkgs/foo-lib/BUILD.bazel
  +++ b/pkgs/foo-lib/BUILD.bazel
  @@ -20,6 +20,7 @@ ts_project(
       name = "typescript",
       srcs = [
           "src/index.ts",
  +        "dist/r1.d.ts",
       ],
       declaration = True,
       out_dir = "dist",
  ```
- Run `bazel build //pkgs/foo-lib:typescript` and observe that the project type checks successfully