# alias_trie
`alias_trie` is a trie that supports nodes with aliases. One or more aliases can be set for each node so that the stored value can be retrieved through the alias. Unfortunately, the aliases in the search path may conflict, making it impossible to locate a specific node. This situation is called grammatical ambiguity, and the search cannot be completed using ambiguity grammar.
`alias_trie` can easily and quickly deal with grammatical ambiguities caused by abbreviations, abbreviations or abbreviations in daily grammar, and provide correct and fast retrieval capabilities.
```nothing
                        ROOT
                     ↙        ↘
                A(a): 1        G: 5
            ↙      ↓     ↘                // The alias "f" conflicts.
          B       C: 4    D(d,dd): 2       // The path "A->d->f" is an ambiguous grammar.
          ↓              ↙       ↘
      E(eee): 2     F1(f): 3    F2(f,f2): 4
```
# Usage
This crate is [on crates.io](https://crates.io/crates/alias_trie) and can be
used by adding `alias_trie` to your dependencies in your project's `Cargo.toml`.
```toml
[dependencies]
alias_trie = "0.9"
```
# Examples
```rust
use alias_trie::Trie;
use alias_trie::UniqueOption;

let mut trie = Trie::new();

trie.insert(&["A"], 1);
trie.insert(&["A", "B", "E"], 2);
trie.insert(&["A", "C"], 4);
trie.insert(&["A", "D"], 2);
trie.insert(&["A", "D", "F1"], 3);
trie.insert(&["A", "D", "F2"], 4);
trie.insert(&["G"], 5);

trie.update_aliases(&["A", "B", "E"], &[&["a"], &[], &["eee"]]);
trie.update_aliases(&["A", "D", "F1"], &[&[], &["d", "dd"], &["f"]]);
trie.update_aliases(&["A", "D", "F2"], &[&[], &[], &["f", "f2"]]);

assert_eq!(trie.get(&["a", "d", "F1"]), UniqueOption::Some(&3));
assert_eq!(trie.get(&["a", "d", "f2"]), UniqueOption::Some(&4));
assert_eq!(trie.get(&["a", "d", "f"]), UniqueOption::NonUnique);
assert_eq!(trie.get(&["a", "d", "g"]), UniqueOption::None);
```
