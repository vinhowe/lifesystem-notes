# quocofs

## Rust lib

Just handles local stuff. Very much "plumbing"-focused for now.

Should be able to:
  - read/write hashes file
  - read/write names file
  - generate a key given a salt and plaintext password
  - take data, smush with key
  - take data, unsmush with key

It seems to me like we can start with functions that handle raw data and just build from that where we're doing something common that we want to move down to Rust.

I want the rust lib to handle the filesystem layer of the whole thing. I don't want the Python lib to have to handle anything but the higher level systems but that doesn't seem practical.

## File data formats

### `digests` file

This file's _only job_ is to store the sha256 digests of documents to check if they've changed.

Inspired by [Git's tree file format](https://www.dulwich.io/docs/tutorial/file-format.html#the-tree).

In sort-of-BNF:

```sh
hashes = quoco-digests NULL <item>+
item = <id> NULL <sha256>
```

Where `id` and `sha256` are hex values in binary form.

### `names` file

`names` stores a basic map between plaintext names and random document IDs. Names are optional by design, which makes
child applications responsible for keeping references to documents. This creates a tree of references, where top-level
indexes are referenced in `names`.

Note also that every document will have a digest, but not every document will have a name.

```sh
names = quoco-names NULL <item>+
item = <name> NULL <id>
```

Where `name` is an alphanumeric + `['_', '-', '.']` string, and `id` is a hex value in binary form.

## Notes

Maybe it's worth looking into https://www.sqlite.org/fileformat.html to see if we can use b-trees to optimize retrieval for bigger indexes.
