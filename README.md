![Build Status](https://travis-ci.org/xpepermint/merkle-tree-proof.svg?branch=master)&nbsp;[![NPM Version](https://badge.fury.io/js/merkle-tree-proof.svg)](https://badge.fury.io/js/merkle-tree-proof)

This is a merkle tree proof implementation for calculating merkle root hash from an array of values and exposing merkle leafs. It's a lightweight open source package for the **server** and **browser** (using module bundler), written with [TypeScript](https://www.typescriptlang.org). The source code is available on [GitHub](https://github.com/xpepermint/merkle-tree-proof) where you can also find our [issue tracker](https://github.com/xpepermint/merkle-tree-proof/issues).

## Getting started

First, create an array of values.

| Index | Type | Description
|-|-|-
| 0 | String | First name of a user.
| 1 | String | Middle name of a user.
| 2 | String | Last name of a user.
| 3 | String | Email address of a user.
| 4 | Integer | Age of a user.

This can be converted into a merkle tree where leafs represent the values and the root represents the proof.

```
                    [0,0]             
                    99999               MerkleNode
            [1,0]               [1,1]
            88888               55555   MerkleNode
    [2,0]           [2,1]       [2,2]
    66666           77777       55555   MerkleNode
[3,0]   [3,1]   [3,2]   [3,3]   [3,4]
11111   22222   33333   44444   55555   MerkleNode
-----   -----   -----   -----   —----
  a       b       c       d       e     MerkleValue
```

## Usage

Create an instance of a merkle tree.

```js
import { createHash } from 'crypto'; 
import { Merkle } from '@0xcert/merkle'; 

const merkle = new Merkle({
  algo: (v) => createHash(`sha256`).update(v).digest('hex'),
});
```

Build merkle tree nodes from a list of values.

```ts
const allNodes = merkle.build([
  { index: 0, value: 'a' },
  { index: 1, value: 'b' },
  { index: 2, value: 'c' },
  { index: 3, value: 'd' },
  { index: 4, value: 'e' },
]);
```

Create a minimal list of nodes from which a tree root can be calculated.

```ts
const recipeNodes = await merkle.pack(
  allNodes, 
  [0, 2] // expose values `a` and `c`
);
```

Recalculate the tree root hash object.

```ts
const rootNode = await merkle.calculate([
  // values
  { index: 0, value: 'a' },
], [
  // nodes
  { level: 1, index: 0, hash: '0x...' },
  { level: 1, index: 1, hash: '0x...' },
  { level: 2, index: 0, hash: '0x...' },
], 4);
```

## License (MIT)

```
Copyright (c) 2017+ Kristijan Sedlak <xpepermint@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated modelation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```
