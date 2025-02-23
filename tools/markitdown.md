# Transform the PDF File to a Markdown File

Here we use the Markitdown tool [Markitdown](https://github.com/microsoft/markitdown) to transform the PDF file to a Markdown file. This tool allows for efficient conversion and maintains the formatting of the original document.

## Usage

first, install the tool using the following command:

```bash
git clone git@github.com:microsoft/markitdown.git
cd markitdown
pip install -e packages/markitdown
```

Then, you can use the tool to convert the PDF file to a Markdown file using the following command:

```bash
markitdown input.pdf -o output.md

find . -type f -name '*.pdf' -exec sh -c 'markitdown "{}" -o "{}.md"' \;
```
