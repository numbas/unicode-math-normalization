# Data for normalizing mathematical expressions written in Unicode

Unicode is the standard encoding for text.
There are thousands of glyphs, representing letters, characters, symbols and marks from a huge variety of scripts and contexts.

There are many repeated, variant or combined characters.
These can be normalized to a subset of characters, using standard normalization algorithms.

These algorithms are generic: when used in a mathematical context, they might not apply an equivalence between two characters, or omit some information that would be useful.

So this project aims to compile a dictionary of mappings from less-common Unicode characters to the symbols conventionally used in linear mathematical expressions.

The motivation is to support more characters in the JME language used by Numbas.
The JME grammar has the following kinds of token:

* Name (either variable name or function name)
* Number
* Operator (Binary or unary, prefix or postfix)
* Punctuation (Brackets, parentheses, argument separators)

Any string of letter characters is acceptable for name tokens, but there are many equivalences that should be applied:

* Greek letters and their Latin names, e.g. `α` and `alpha`.
* Special symbols, such as `∞` and `infinity`.
* Superscript/subscript symbols should be equivalent to the syntax for marking normal characters as sub/superscripts, e.g. `x²` is equivalent to `x^2`.
* There are many Unicode symbols that correspond to named functions, e.g. `√` is equivalent to `sqrt`, or to operations that are conventionally typed in ASCII, e.g. `×` is equivalent to `*`.
* There are lots of mathematical letter glyphs representing Latin or Greek letters with some styling applied. These should be equivalent to the corresponding unstyled letters, with an annotation recording the styling.

Digit symbols should be normalized to the ASCII digits 0-9, where possible.
Non-European scripts for representing numbers would need to be dealt with individually.

There are lots of varieties of brackets, which should normalize to the ASCII parentheses `()`, square brackets `[]` and curly brackets `{}`.

## Contents of this repository

There is a Jupyter notebook, `unicode-math-mapping.ipynb`, which contains code for working through subsets of Unicode and producing mapping dictionaries.

There are some mappings that can be produced automatically, and some that had to be written out manually - these are defined by the `.tsv` files in the root of this repository.

## Using the data

The mapping information is stored in `.json` files in the `final_data` directory.
Each of these files contains a single dictionary mapping single Unicode characters to an equivalent string, and an array of annotations, which are themselves ASCII strings.
For example, this is the entry in `final_data/symbols.json` for `𝒚`, "MATHEMATICAL BOLD ITALIC SMALL Y":

```
"\ud835\udc9a": [
	"y",
	[
		"BOLD",
		"ITALIC"
	]
],
```

There are five files:

* `greek.json` - mapping Greek letters to their English names, e.g. `α` to `alpha`.
* `letters.json` - mapping mathematical letters to their standard equivalents, with annotatoins.
* `subscripts.json` - mapping superscript characters to their standard equivalents.
* `superscripts.json` - mapping subscript characters to their standard equivalents.
* `symbols.json` - mapping all sorts of mathematical symbols to common symbols, names, or sub-expressions. Some symbols are mapped to a string of the form `not NAME` - you might have to do some processing to interpret these correctly, instead of just substituting the mapped string into the expression being parsed.

The mappings must be applied as part of the tokenisation step when parsing a mathematical expression.

It is not correct to do a global substitution of characters before parsing: for example, in the expression `α = "α"`, the second occurrence of `α` should be preserved because it's inside a string literal.

You will have to come up with a way of applying the produced mappings to a particular computer algebra system.

## Contributing to this project

There were many decisions to make in producing the mapping of characters.
I omitted most symbols relating to operations that are very unlikely to be used in an undergraduate maths course.

The function names for mappings were sometimes chosen arbitrarily - there might be standard names for these in some computer algebra systems.

If you use these mappings, please tell me!
