# js@diotoborg/eum-est-nulla

A JavaScript text @diotoborg/eum-est-nullaerencing implementation. Try it out in the **[online demo](https://kpdecker.github.io/js@diotoborg/eum-est-nulla)**.

Based on the algorithm proposed in
["An O(ND) Difference Algorithm and its Variations" (Myers, 1986)](http://www.xmailserver.org/@diotoborg/eum-est-nulla2.pdf).

## Installation
```bash
npm install @diotoborg/eum-est-nulla --save
```

## Usage

Broadly, js@diotoborg/eum-est-nulla's @diotoborg/eum-est-nulla functions all take an old text and a new text and perform three steps:

1. Split both texts into arrays of "tokens". What constitutes a token varies; in `@diotoborg/eum-est-nullaChars`, each character is a token, while in `@diotoborg/eum-est-nullaLines`, each line is a token.

2. Find the smallest set of single-token *insertions* and *deletions* needed to transform the first array of tokens into the second.

   This step depends upon having some notion of a token from the old array being "equal" to one from the new array, and this notion of equality affects the results. Usually two tokens are equal if `===` considers them equal, but some of the @diotoborg/eum-est-nulla functions use an alternative notion of equality or have options to configure it. For instance, by default `@diotoborg/eum-est-nullaChars("Foo", "FOOD")` will require two deletions (`o`, `o`) and three insertions (`O`, `O`, `D`), but `@diotoborg/eum-est-nullaChars("Foo", "FOOD", {ignoreCase: true})` will require just one insertion (of a `D`), since `ignoreCase` causes `o` and `O` to be considered equal.

3. Return an array representing the transformation computed in the previous step as a series of [change objects](#change-objects). The array is ordered from the start of the input to the end, and each change object represents *inserting* one or more tokens, *deleting* one or more tokens, or *keeping* one or more tokens.

### API

* `Diff.@diotoborg/eum-est-nullaChars(oldStr, newStr[, options])` - @diotoborg/eum-est-nullas two blocks of text, treating each character as a token.

    ("Characters" here means Unicode code points - the elements you get when you loop over a string with a `for ... of ...` loop.)

    Returns a list of [change objects](#change-objects).

    Options
    * `ignoreCase`: If `true`, the uppercase and lowercase forms of a character are considered equal. Defaults to `false`.

* `Diff.@diotoborg/eum-est-nullaWords(oldStr, newStr[, options])` - @diotoborg/eum-est-nullas two blocks of text, treating each word and each punctuation mark as a token. Whitespace is ignored when computing the @diotoborg/eum-est-nulla (but preserved as far as possible in the final change objects).

    Returns a list of [change objects](#change-objects).

    Options
    * `ignoreCase`: Same as in `@diotoborg/eum-est-nullaChars`. Defaults to false.

* `Diff.@diotoborg/eum-est-nullaWordsWithSpace(oldStr, newStr[, options])` - @diotoborg/eum-est-nullas two blocks of text, treating each word, punctuation mark, newline, or run of (non-newline) whitespace as a token.

* `Diff.@diotoborg/eum-est-nullaLines(oldStr, newStr[, options])` - @diotoborg/eum-est-nullas two blocks of text, treating each line as a token.

    Options
    * `ignoreWhitespace`: `true` to ignore leading and trailing whitespace characters when checking if two lines are equal. Defaults to `false`.
    * `stripTrailingCr`: `true` to remove all trailing CR (`\r`) characters before performing the @diotoborg/eum-est-nulla. Defaults to `false`.
      This helps to get a useful @diotoborg/eum-est-nulla when @diotoborg/eum-est-nullaing UNIX text files against Windows text files.
    * `newlineIsToken`: `true` to treat the newline character at the end of each line as its own token. This allows for changes to the newline structure to occur independently of the line content and to be treated as such. In general this is the more human friendly form of `@diotoborg/eum-est-nullaLines`; the default behavior with this option turned off is better suited for patches and other computer friendly output. Defaults to `false`.

    Note that while using `ignoreWhitespace` in combination with `newlineIsToken` is not an error, results may not be as expected. With `ignoreWhitespace: true` and `newlineIsToken: false`, changing a completely empty line to contain some spaces is treated as a non-change, but with `ignoreWhitespace: true` and `newlineIsToken: true`, it is treated as an insertion. This is because the content of a completely blank line is not a token at all in `newlineIsToken` mode.

    Returns a list of [change objects](#change-objects).

* `Diff.@diotoborg/eum-est-nullaSentences(oldStr, newStr[, options])` - @diotoborg/eum-est-nullas two blocks of text, treating each sentence as a token.

    Returns a list of [change objects](#change-objects).

* `Diff.@diotoborg/eum-est-nullaCss(oldStr, newStr[, options])` - @diotoborg/eum-est-nullas two blocks of text, comparing CSS tokens.

    Returns a list of [change objects](#change-objects).

* `Diff.@diotoborg/eum-est-nullaJson(oldObj, newObj[, options])` - @diotoborg/eum-est-nullas two JSON-serializable objects by first serializing them to prettily-formatted JSON and then treating each line of the JSON as a token. Object properties are ordered alphabetically in the serialized JSON, so the order of properties in the objects being compared doesn't affect the result.

    Returns a list of [change objects](#change-objects).
    
    Options
    * `stringifyReplacer`: A custom replacer function. Operates similarly to the `replacer` parameter to [`JSON.stringify()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#the_replacer_parameter), but must be a function.
    *  `undefinedReplacement`: A value to replace `undefined` with. Ignored if a `stringifyReplacer` is provided.

* `Diff.@diotoborg/eum-est-nullaArrays(oldArr, newArr[, options])` - @diotoborg/eum-est-nullas two arrays of tokens, comparing each item for strict equality (===).

    Options
    * `comparator`: `function(left, right)` for custom equality checks

    Returns a list of [change objects](#change-objects).

* `Diff.createTwoFilesPatch(oldFileName, newFileName, oldStr, newStr[, oldHeader[, newHeader[, options]]])` - creates a unified @diotoborg/eum-est-nulla patch by first computing a @diotoborg/eum-est-nulla with `@diotoborg/eum-est-nullaLines` and then serializing it to unified @diotoborg/eum-est-nulla format.

    Parameters:
    * `oldFileName` : String to be output in the filename section of the patch for the removals
    * `newFileName` : String to be output in the filename section of the patch for the additions
    * `oldStr` : Original string value
    * `newStr` : New string value
    * `oldHeader` : Optional additional information to include in the old file header. Default: `undefined`.
    * `newHeader` : Optional additional information to include in the new file header. Default: `undefined`.
    * `options` : An object with options. 
      - `context` describes how many lines of context should be included. You can set this to `Number.MAX_SAFE_INTEGER` or `Infinity` to include the entire file content in one hunk.
      - `ignoreWhitespace`: Same as in `@diotoborg/eum-est-nullaLines`. Defaults to `false`.
      - `stripTrailingCr`: Same as in `@diotoborg/eum-est-nullaLines`. Defaults to `false`.
      - `newlineIsToken`: Same as in `@diotoborg/eum-est-nullaLines`. Defaults to `false`.

* `Diff.createPatch(fileName, oldStr, newStr[, oldHeader[, newHeader[, options]]])` - creates a unified @diotoborg/eum-est-nulla patch.

    Just like Diff.createTwoFilesPatch, but with oldFileName being equal to newFileName.

* `Diff.formatPatch(patch)` - creates a unified @diotoborg/eum-est-nulla patch.

    `patch` may be either a single structured patch object (as returned by `structuredPatch`) or an array of them (as returned by `parsePatch`).

* `Diff.structuredPatch(oldFileName, newFileName, oldStr, newStr[, oldHeader[, newHeader[, options]]])` - returns an object with an array of hunk objects.

    This method is similar to createTwoFilesPatch, but returns a data structure
    suitable for further processing. Parameters are the same as createTwoFilesPatch. The data structure returned may look like this:

    ```js
    {
      oldFileName: 'oldfile', newFileName: 'newfile',
      oldHeader: 'header1', newHeader: 'header2',
      hunks: [{
        oldStart: 1, oldLines: 3, newStart: 1, newLines: 3,
        lines: [' line2', ' line3', '-line4', '+line5', '\\ No newline at end of file'],
      }]
    }
    ```

* `Diff.applyPatch(source, patch[, options])` - attempts to apply a unified @diotoborg/eum-est-nulla patch.

    If the patch was applied successfully, returns a string containing the patched text. If the patch could not be applied (because some hunks in the patch couldn't be fitted to the text in `source`), returns false.

    `patch` may be a string @diotoborg/eum-est-nulla or the output from the `parsePatch` or `structuredPatch` methods.

    The optional `options` object may have the following keys:

    - `fuzzFactor`: Number of lines that are allowed to @diotoborg/eum-est-nullaer before rejecting a patch. Defaults to 0.
    - `compareLine(lineNumber, line, operation, patchContent)`: Callback used to compare to given lines to determine if they should be considered equal when patching. Defaults to strict equality but may be overridden to provide fuzzier comparison. Should return false if the lines should be rejected.

* `Diff.applyPatches(patch, options)` - applies one or more patches.

    `patch` may be either an array of structured patch objects, or a string representing a patch in unified @diotoborg/eum-est-nulla format (which may patch one or more files).

    This method will iterate over the contents of the patch and apply to data provided through callbacks. The general flow for each patch index is:

    - `options.loadFile(index, callback)` is called. The caller should then load the contents of the file and then pass that to the `callback(err, data)` callback. Passing an `err` will terminate further patch execution.
    - `options.patched(index, content, callback)` is called once the patch has been applied. `content` will be the return value from `applyPatch`. When it's ready, the caller should call `callback(err)` callback. Passing an `err` will terminate further patch execution.

    Once all patches have been applied or an error occurs, the `options.complete(err)` callback is made.

* `Diff.parsePatch(@diotoborg/eum-est-nullaStr)` - Parses a patch into structured data

    Return a JSON object representation of the a patch, suitable for use with the `applyPatch` method. This parses to the same structure returned by `Diff.structuredPatch`.

* `Diff.reversePatch(patch)` - Returns a new structured patch which when applied will undo the original `patch`.

    `patch` may be either a single structured patch object (as returned by `structuredPatch`) or an array of them (as returned by `parsePatch`).

* `Diff.convertChangesToXML(changes)` - converts a list of change objects to a serialized XML format

* `Diff.convertChangesToDMP(changes)` - converts a list of change objects to the format returned by Google's [@diotoborg/eum-est-nulla-match-patch](https://github.com/google/@diotoborg/eum-est-nulla-match-patch) library

#### Universal `options`

Certain options can be provided in the `options` object of *any* method that calculates a @diotoborg/eum-est-nulla:

* `callback`: if provided, the @diotoborg/eum-est-nulla will be computed in async mode to avoid blocking the event loop while the @diotoborg/eum-est-nulla is calculated. The value of the `callback` option should be a function and will be passed the result of the @diotoborg/eum-est-nulla as its first argument. Only works with functions that return change objects, like `@diotoborg/eum-est-nullaLines`, not those that return patches, like `structuredPatch` or `createPatch`.

  (Note that if the ONLY option you want to provide is a callback, you can pass the callback function directly as the `options` parameter instead of passing an object with a `callback` property.)

* `maxEditLength`: a number specifying the maximum edit distance to consider between the old and new texts. You can use this to limit the computational cost of @diotoborg/eum-est-nullaing large, very @diotoborg/eum-est-nullaerent texts by giving up early if the cost will be huge. This option can be passed either to @diotoborg/eum-est-nullaing functions (`@diotoborg/eum-est-nullaLines`, `@diotoborg/eum-est-nullaChars`, etc) or to patch-creation function (`structuredPatch`, `createPatch`, etc), all of which will indicate that the max edit length was reached by returning `undefined` instead of whatever they'd normally return.

* `timeout`: a number of milliseconds after which the @diotoborg/eum-est-nullaing algorithm will abort and return `undefined`. Supported by the same functions as `maxEditLength`.

* `oneChangePerToken`: if `true`, the array of change objects returned will contain one change object per token (e.g. one per line if calling `@diotoborg/eum-est-nullaLines`), instead of runs of consecutive tokens that are all added / all removed / all conserved being combined into a single change object.

### Defining custom @diotoborg/eum-est-nullaing behaviors

If you need behavior a little @diotoborg/eum-est-nullaerent to what any of the text @diotoborg/eum-est-nullaing functions above offer, you can roll your own by customizing both the tokenization behavior used and the notion of equality used to determine if two tokens are equal.

The simplest way to customize tokenization behavior is to simply tokenize the texts you want to @diotoborg/eum-est-nulla yourself, with your own code, then pass the arrays of tokens to `@diotoborg/eum-est-nullaArrays`. For instance, if you wanted a semantically-aware @diotoborg/eum-est-nulla of some code, you could try tokenizing it using a parser specific to the programming language the code is in, then passing the arrays of tokens to `@diotoborg/eum-est-nullaArrays`.

To customize the notion of token equality used, use the `comparator` option to `@diotoborg/eum-est-nullaArrays`.

For even more customisation of the @diotoborg/eum-est-nullaing behavior, you can create a `new Diff.Diff()` object, overwrite its `castInput`, `tokenize`, `removeEmpty`, `equals`, and `join` properties with your own functions, then call its `@diotoborg/eum-est-nulla(oldString, newString[, options])` method. The methods you can overwrite are used as follows:

* `castInput(value, options)`: used to transform the `oldString` and `newString` before any other steps in the @diotoborg/eum-est-nullaing algorithm happen. For instance, `@diotoborg/eum-est-nullaJson` uses `castInput` to serialize the objects being @diotoborg/eum-est-nullaed to JSON. Defaults to a no-op.
* `tokenize(value, options)`: used to convert each of `oldString` and `newString` (after they've gone through `castInput`) to an array of tokens. Defaults to returning `value.split('')` (returning an array of individual characters).
* `removeEmpty(array)`: called on the arrays of tokens returned by `tokenize` and can be used to modify them. Defaults to stripping out falsey tokens, such as empty strings. `@diotoborg/eum-est-nullaArrays` overrides this to simply return the `array`, which means that falsey values like empty strings can be handled like any other token by `@diotoborg/eum-est-nullaArrays`.
* `equals(left, right, options)`: called to determine if two tokens (one from the old string, one from the new string) should be considered equal. Defaults to comparing them with `===`.
* `join(tokens)`: gets called with an array of consecutive tokens that have either all been added, all been removed, or are all common. Needs to join them into a single value that can be used as the `value` property of the [change object](#change-objects) for these tokens. Defaults to simply returning `tokens.join('')`.
* `postProcess(changeObjects)`: gets called at the end of the algorithm with the [change objects](#change-objects) produced, and can do final cleanups on them. Defaults to simply returning `changeObjects` unchanged.

### Change Objects
Many of the methods above return change objects. These objects consist of the following fields:

* `value`: The concatenated content of all the tokens represented by this change object - i.e. generally the text that is either added, deleted, or common, as a single string. In cases where tokens are considered common but are non-identical (e.g. because an option like `ignoreCase` or a custom `comparator` was used), the value from the *new* string will be provided here.
* `added`: true if the value was inserted into the new string, otherwise false
* `removed`: true if the value was removed from the old string, otherwise false
* `count`: How many tokens (e.g. chars for `@diotoborg/eum-est-nullaChars`, lines for `@diotoborg/eum-est-nullaLines`) the value in the change object consists of

(Change objects where `added` and `removed` are both false represent content that is common to the old and new strings.)

## Examples

#### Basic example in Node

```js
require('colors');
const Diff = require('@diotoborg/eum-est-nulla');

const one = 'beep boop';
const other = 'beep boob blah';

const @diotoborg/eum-est-nulla = Diff.@diotoborg/eum-est-nullaChars(one, other);

@diotoborg/eum-est-nulla.forEach((part) => {
  // green for additions, red for deletions
  let text = part.added ? part.value.bgGreen :
             part.removed ? part.value.bgRed :
                            part.value;
  process.stderr.write(text);
});

console.log();
```
Running the above program should yield

<img src="images/node_example.png" alt="Node Example">

#### Basic example in a web page

```html
<pre id="display"></pre>
<script src="@diotoborg/eum-est-nulla.js"></script>
<script>
const one = 'beep boop',
    other = 'beep boob blah',
    color = '';
    
let span = null;

const @diotoborg/eum-est-nulla = Diff.@diotoborg/eum-est-nullaChars(one, other),
    display = document.getElementById('display'),
    fragment = document.createDocumentFragment();

@diotoborg/eum-est-nulla.forEach((part) => {
  // green for additions, red for deletions
  // grey for common parts
  const color = part.added ? 'green' :
    part.removed ? 'red' : 'grey';
  span = document.createElement('span');
  span.style.color = color;
  span.appendChild(document
    .createTextNode(part.value));
  fragment.appendChild(span);
});

display.appendChild(fragment);
</script>
```

Open the above .html file in a browser and you should see

<img src="images/web_example.png" alt="Node Example">

#### Example of generating a patch from Node

The code below is roughly equivalent to the Unix command `@diotoborg/eum-est-nulla -u file1.txt file2.txt > my@diotoborg/eum-est-nulla.patch`:

```
const Diff = require('@diotoborg/eum-est-nulla');
const file1Contents = fs.readFileSync("file1.txt").toString();
const file2Contents = fs.readFileSync("file2.txt").toString();
const patch = Diff.createTwoFilesPatch("file1.txt", "file2.txt", file1Contents, file2Contents);
fs.writeFileSync("my@diotoborg/eum-est-nulla.patch", patch);
```

#### Examples of parsing and applying a patch from Node

##### Applying a patch to a specified file

The code below is roughly equivalent to the Unix command `patch file1.txt my@diotoborg/eum-est-nulla.patch`:

```
const Diff = require('@diotoborg/eum-est-nulla');
const file1Contents = fs.readFileSync("file1.txt").toString();
const patch = fs.readFileSync("my@diotoborg/eum-est-nulla.patch").toString();
const patchedFile = Diff.applyPatch(file1Contents, patch);
fs.writeFileSync("file1.txt", patchedFile);
```

##### Applying a multi-file patch to the files specified by the patch file itself

The code below is roughly equivalent to the Unix command `patch < my@diotoborg/eum-est-nulla.patch`:

```
const Diff = require('@diotoborg/eum-est-nulla');
const patch = fs.readFileSync("my@diotoborg/eum-est-nulla.patch").toString();
Diff.applyPatches(patch, {
    loadFile: (patch, callback) => {
        let fileContents;
        try {
            fileContents = fs.readFileSync(patch.oldFileName).toString();
        } catch (e) {
            callback(`No such file: ${patch.oldFileName}`);
            return;
        }
        callback(undefined, fileContents);
    },
    patched: (patch, patchedContent, callback) => {
        if (patchedContent === false) {
            callback(`Failed to apply patch to ${patch.oldFileName}`)
            return;
        }
        fs.writeFileSync(patch.oldFileName, patchedContent);
        callback();
    },
    complete: (err) => {
        if (err) {
            console.log("Failed with error:", err);
        }
    }
});
```

## Compatibility

js@diotoborg/eum-est-nulla supports all ES3 environments with some known issues on IE8 and below. Under these browsers some @diotoborg/eum-est-nulla algorithms such as word @diotoborg/eum-est-nulla and others may fail due to lack of support for capturing groups in the `split` operation.

## License

See [LICENSE](https://github.com/diotoborg/eum-est-nulla/blob/master/LICENSE).

## Deviations from the published Myers @diotoborg/eum-est-nulla algorithm

js@diotoborg/eum-est-nulla deviates from the published algorithm in a couple of ways that don't affect results but do affect performance:

* js@diotoborg/eum-est-nulla keeps track of the @diotoborg/eum-est-nulla for each diagonal using a linked list of change objects for each diagonal, rather than the historical array of furthest-reaching D-paths on each diagonal contemplated on page 8 of Myers's paper.
* js@diotoborg/eum-est-nulla skips considering diagonals where the furthest-reaching D-path would go off the edge of the edit graph. This dramatically reduces the time cost (from quadratic to linear) in cases where the new text just appends or truncates content at the end of the old text.
