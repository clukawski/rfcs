- Feature Name: cfg-shortcuts
- Start Date: 2015-10-23
- RFC PR: (leave this empty)
- Rust Issue: (leave this empty)

# Summary
[summary]: #summary

Allow defining new #[cfg] variables based on existing ones.

# Motivation
[motivation]: #motivation

It's quite often that a cfg directive in an attribute becomes fairy complicated when support for more platforms are added. 

- [Example 1](https://github.com/rust-lang/rust/blob/d689182e5d271f76604c951b6004546a3a7f0c0c/src/libstd/sys/common/unwind/gcc.rs#L77)
- [Example 2](https://github.com/rust-lang/rust/blob/d689182e5d271f76604c951b6004546a3a7f0c0c/src/liballoc_jemalloc/lib.rs#L37)
- [Example 3](https://github.com/rust-lang/rust/blob/d689182e5d271f76604c951b6004546a3a7f0c0c/src/libstd/sys/common/unwind/mod.rs#L80)

# Detailed design
[design]: #detailed-design

## Allowed locations

The `#[cfg_shortcut(...)]`  attribute may be placed in the same locations as allowed for the `#[cfg]` attributes, i.e. on items and modules.

## Allowed usage

Same locations as normal cfg variables, i.e. in conditions associated with `#[cfg]`, `#[cfg_attr]`, `#[cfg_shortcut]` attributes, as well as `cfg!()` macros.

## Scope

A meta-variable created using `#[cfg_shortcut(...)]` exists in the scope of the item it is attached to and ceases to exist once this scope is closed.

## Visibility

Meta-variables are visible in scope the item they are attached to, as well in all in and of all its descendants.

may be used in conditions associated with `#[cfg]`, `#[cfg_attr]` and other `#[cfg_shortcut]` attributes, as well as `cfg!()` macros located inside the item that meta-variable definition is attached to, as well as in and of all its descendants.

## Order of evaluation

Meta-variables are evaluated in the order they appear in source.  In the following example, the second `#[cfg_shortcut]` directive sees the effect of the first one.
```rust
#[cfg_shortcut(all(target_os="windows", target_arch="x86_64"), win64)]
#[cfg_shortcut(any(win64, target_env="msvc"), seh_exception_model)]
```
## Shadowing

Meta-variables shadow meta-variables of the same name from outer scopes.  Once the current scope ends, the value of the outer variable is restored.

Meta-variables also shadow meta-variables of the same name defined earlier in the same scope.
Rationale: by analogy with `let` bindings.

# Drawbacks
[drawbacks]: #drawbacks

Why should we *not* do this?

# Alternatives
[alternatives]: #alternatives

What other designs have been considered? What is the impact of not doing this?

# Unresolved questions
[unresolved]: #unresolved-questions

What parts of the design are still TBD?
