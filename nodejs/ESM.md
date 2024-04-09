# ESM

## Resolution Algorithm Specification

### ESM_RESOLVE(specifier, parentURL)

Let resolved be undefined.
If specifier is a valid URL, then
Set resolved to the result of parsing and reserializing specifier as a URL.
Otherwise, if specifier starts with "/", "./", or "../", then
Set resolved to the URL resolution of specifier relative to parentURL.
Otherwise, if specifier starts with "#", then
Set resolved to the result of PACKAGE_IMPORTS_RESOLVE(specifier, parentURL, defaultConditions).
Otherwise,
Note: specifier is now a bare specifier.
Set resolved the result of PACKAGE_RESOLVE(specifier, parentURL).
Let format be undefined.
If resolved is a "file:" URL, then
If resolved contains any percent encodings of "/" or "\" ("%2F" and "%5C" respectively), then
Throw an Invalid Module Specifier error.
If the file at resolved is a directory, then
Throw an Unsupported Directory Import error.
If the file at resolved does not exist, then
Throw a Module Not Found error.
Set resolved to the real path of resolved, maintaining the same URL querystring and fragment components.
Set format to the result of ESM_FILE_FORMAT(resolved).
Otherwise,
Set format the module format of the content type associated with the URL resolved.
Return format and resolved to the loading phase

### PACKAGE_RESOLVE(packageSpecifier, parentURL)

Let packageName be undefined.
If packageSpecifier is an empty string, then
Throw an Invalid Module Specifier error.
If packageSpecifier is a Node.js builtin module name, then
Return the string "node:" concatenated with packageSpecifier.
If packageSpecifier does not start with "@", then
Set packageName to the substring of packageSpecifier until the first "/" separator or the end of the string.
Otherwise,
If packageSpecifier does not contain a "/" separator, then
Throw an Invalid Module Specifier error.
Set packageName to the substring of packageSpecifier until the second "/" separator or the end of the string.
If packageName starts with "." or contains "\" or "%", then
Throw an Invalid Module Specifier error.
Let packageSubpath be "." concatenated with the substring of packageSpecifier from the position at the length of packageName.
If packageSubpath ends in "/", then
Throw an Invalid Module Specifier error.
Let selfUrl be the result of PACKAGE_SELF_RESOLVE(packageName, packageSubpath, parentURL).
If selfUrl is not undefined, return selfUrl.
While parentURL is not the file system root,
Let packageURL be the URL resolution of "node_modules/" concatenated with packageSpecifier, relative to parentURL.
Set parentURL to the parent folder URL of parentURL.
If the folder at packageURL does not exist, then
Continue the next loop iteration.
Let pjson be the result of READ_PACKAGE_JSON(packageURL).
If pjson is not null and pjson.exports is not null or undefined, then
Return the result of PACKAGE_EXPORTS_RESOLVE(packageURL, packageSubpath, pjson.exports, defaultConditions).
Otherwise, if packageSubpath is equal to ".", then
If pjson.main is a string, then
Return the URL resolution of main in packageURL.
Otherwise,
Return the URL resolution of packageSubpath in packageURL.
Throw a Module Not Found error.

### PACKAGE_SELF_RESOLVE(packageName, packageSubpath, parentURL)

Let packageURL be the result of LOOKUP_PACKAGE_SCOPE(parentURL).
If packageURL is null, then
Return undefined.
Let pjson be the result of READ_PACKAGE_JSON(packageURL).
If pjson is null or if pjson.exports is null or undefined, then
Return undefined.
If pjson.name is equal to packageName, then
Return the result of PACKAGE_EXPORTS_RESOLVE(packageURL, packageSubpath, pjson.exports, defaultConditions).
Otherwise, return undefined.

### PACKAGE_EXPORTS_RESOLVE(packageURL, subpath, exports, conditions) {#PACKAGE_EXPORTS_RESOLVE}

1. If *exports* is an Object with both a key starting with "." and a key not starting with ".", throw an *Invalid Package Configuration error*.
如果 *exports* 是一个对象（有一个以"."开头的key和一个不以"."开头的key，抛出一个无效的包配置错误）
2. If *subpath* is equal to ".", then
如果 *subpath* 等于".",则
    1. Let *mainExport* be **undefined**.
    让*mainExport* = **undefined**
    2. If *exports* is a String or Array, or an Object containing no keys starting with ".", then
    如果 *exports* 是一个字符串或者一个数组，或者是没有以"."为开头的key的对象，则
        1. Set *mainExport* to *exports*.
        怎让*mainExport*=*export*
    3. Otherwise if *exports* is an Object containing a "." property, then
    如果 *export* 是1个包含了"."property的对象，则
        1. Set *mainExport* to *exports["."]*.
        则让 *mainExport* = *exports["."]*
    4. If *mainExport* is not undefined, then
    如果 *mainExport* 不是undefined，则
        1. Let *resolved* be the result of PACKAGE_TARGET_RESOLVE( packageURL, mainExport, null, false, conditions).
        让 *resolved* = PACKAGE_TARGET_RESOLVE( packageURL, mainExport, null, false, conditions)
        2. If resolved is not null or undefined, return resolved.
3. Otherwise, if exports is an Object and all keys of exports start with ".", then
    1. Assert: subpath begins with "./".
    2. Let resolved be the result of PACKAGE_IMPORTS_EXPORTS_RESOLVE( subpath, exports, packageURL, false, conditions).
    3. If resolved is not null or undefined, return resolved.
4. Throw a Package Path Not Exported error.

### PACKAGE_IMPORTS_RESOLVE(specifier, parentURL, conditions)

Assert: specifier begins with "#".
If specifier is exactly equal to "#" or starts with "#/", then
Throw an Invalid Module Specifier error.
Let packageURL be the result of LOOKUP_PACKAGE_SCOPE(parentURL).
If packageURL is not null, then
Let pjson be the result of READ_PACKAGE_JSON(packageURL).
If pjson.imports is a non-null Object, then
Let resolved be the result of PACKAGE_IMPORTS_EXPORTS_RESOLVE( specifier, pjson.imports, packageURL, true, conditions).
If resolved is not null or undefined, return resolved.
Throw a Package Import Not Defined error.

### PACKAGE_IMPORTS_EXPORTS_RESOLVE(matchKey, matchObj, packageURL, isImports, conditions)

If matchKey is a key of matchObj and does not contain "*", then
Let target be the value of matchObj[matchKey].
Return the result of PACKAGE_TARGET_RESOLVE(packageURL, target, null, isImports, conditions).
Let expansionKeys be the list of keys of matchObj containing only a single "*", sorted by the sorting function PATTERN_KEY_COMPARE which orders in descending order of specificity.
For each key expansionKey in expansionKeys, do
Let patternBase be the substring of expansionKey up to but excluding the first "*" character.
If matchKey starts with but is not equal to patternBase, then
Let patternTrailer be the substring of expansionKey from the index after the first "*" character.
If patternTrailer has zero length, or if matchKey ends with patternTrailer and the length of matchKey is greater than or equal to the length of expansionKey, then
Let target be the value of matchObj[expansionKey].
Let patternMatch be the substring of matchKey starting at the index of the length of patternBase up to the length of matchKey minus the length of patternTrailer.
Return the result of PACKAGE_TARGET_RESOLVE(packageURL, target, patternMatch, isImports, conditions).
Return null.

### PATTERN_KEY_COMPARE(keyA, keyB)

Assert: keyA ends with "/" or contains only a single "*".
Assert: keyB ends with "/" or contains only a single "*".
Let baseLengthA be the index of "*" in keyA plus one, if keyA contains "*", or the length of keyA otherwise.
Let baseLengthB be the index of "*" in keyB plus one, if keyB contains "*", or the length of keyB otherwise.
If baseLengthA is greater than baseLengthB, return -1.
If baseLengthB is greater than baseLengthA, return 1.
If keyA does not contain "*", return 1.
If keyB does not contain "*", return -1.
If the length of keyA is greater than the length of keyB, return -1.
If the length of keyB is greater than the length of keyA, return 1.
Return 0.

### PACKAGE_TARGET_RESOLVE(packageURL, target, patternMatch, isImports, conditions)

If target is a String, then
If target does not start with "./", then
If isImports is false, or if target starts with "../" or "/", or if target is a valid URL, then
Throw an Invalid Package Target error.
If patternMatch is a String, then
Return PACKAGE_RESOLVE(target with every instance of "*" replaced by patternMatch, packageURL + "/").
Return PACKAGE_RESOLVE(target, packageURL + "/").
If target split on "/" or "\" contains any "", ".", "..", or "node_modules" segments after the first "." segment, case insensitive and including percent encoded variants, throw an Invalid Package Target error.
Let resolvedTarget be the URL resolution of the concatenation of packageURL and target.
Assert: packageURL is contained in resolvedTarget.
If patternMatch is null, then
Return resolvedTarget.
If patternMatch split on "/" or "\" contains any "", ".", "..", or "node_modules" segments, case insensitive and including percent encoded variants, throw an Invalid Module Specifier error.
Return the URL resolution of resolvedTarget with every instance of "*" replaced with patternMatch.
Otherwise, if target is a non-null Object, then
If target contains any index property keys, as defined in ECMA-262 6.1.7 Array Index, throw an Invalid Package Configuration error.
For each property p of target, in object insertion order as,
If p equals "default" or conditions contains an entry for p, then
Let targetValue be the value of the p property in target.
Let resolved be the result of PACKAGE_TARGET_RESOLVE( packageURL, targetValue, patternMatch, isImports, conditions).
If resolved is equal to undefined, continue the loop.
Return resolved.
Return undefined.
Otherwise, if target is an Array, then
If _target.length is zero, return null.
For each item targetValue in target, do
Let resolved be the result of PACKAGE_TARGET_RESOLVE( packageURL, targetValue, patternMatch, isImports, conditions), continuing the loop on any Invalid Package Target error.
If resolved is undefined, continue the loop.
Return resolved.
Return or throw the last fallback resolution null return or error.
Otherwise, if target is null, return null.
Otherwise throw an Invalid Package Target error.

### ESM_FILE_FORMAT(url)

Assert: url corresponds to an existing file.
If url ends in ".mjs", then
Return "module".
If url ends in ".cjs", then
Return "commonjs".
If url ends in ".json", then
Return "json".
If --experimental-wasm-modules is enabled and url ends in ".wasm", then
Return "wasm".
Let packageURL be the result of LOOKUP_PACKAGE_SCOPE(url).
Let pjson be the result of READ_PACKAGE_JSON(packageURL).
Let packageType be null.
If pjson?.type is "module" or "commonjs", then
Set packageType to pjson.type.
If url ends in ".js", then
If packageType is not null, then
Return packageType.
If --experimental-detect-module is enabled and the source of module contains static import or export syntax, then
Return "module".
Return "commonjs".
If url does not have any extension, then
If packageType is "module" and --experimental-wasm-modules is enabled and the file at url contains the header for a WebAssembly module, then
Return "wasm".
If packageType is not null, then
Return packageType.
If --experimental-detect-module is enabled and the source of module contains static import or export syntax, then
Return "module".
Return "commonjs".
Return undefined (will throw during load phase).

### LOOKUP_PACKAGE_SCOPE(url)

Let scopeURL be url.
While scopeURL is not the file system root,
Set scopeURL to the parent URL of scopeURL.
If scopeURL ends in a "node_modules" path segment, return null.
Let pjsonURL be the resolution of "package.json" within scopeURL.
if the file at pjsonURL exists, then
Return scopeURL.
Return null.

### READ_PACKAGE_JSON(packageURL)

Let pjsonURL be the resolution of "package.json" within packageURL.
If the file at pjsonURL does not exist, then
Return null.
If the file at packageURL does not parse as valid JSON, then
Throw an Invalid Package Configuration error.
Return the parsed JSON source of the file at pjsonURL.