double-checked-cell
===================

A thread-safe lazily initialized cell using double-checked locking.

[![Build Status](https://travis-ci.org/niklasf/double-checked-cell.svg?branch=master)](https://travis-ci.org/niklasf/double-checked-cell)
[![crates.io](https://img.shields.io/crates/v/double-checked-cell.svg)](https://crates.io/crates/double-checked-cell)
[![docs.rs](https://docs.rs/double-checked-cell/badge.svg)](https://crates.io/double-checked-cell)

Introduction
------------

Provides a memory location that can be safely shared between threads and
initialized at most once. Once the cell is initialized it becomes immutable.

If you do not need to change the value after initialization
`DoubleCheckedCell<T>` is more efficient than a `Mutex<Option<T>>`.

```rust
extern crate double_checked_cell;

use double_checked_cell::DoubleCheckedCell;

fn main() {
    let cell = DoubleCheckedCell::new();

    // The cell starts uninitialized.
    assert_eq!(cell.get(), None);

    // Perform potentially expensive initialization.
    let value = cell.get_or_init(|| 21 + 21);
    assert_eq!(*value, 42);
    assert_eq!(cell.get(), Some(&42));

    // The cell is already initialized.
    let value = cell.get_or_init(|| {
        panic!("initilization does not run again")
    });
    assert_eq!(*value, 42);
    assert_eq!(cell.get(), Some(&42));
}
```


Related crates
--------------

These crates are similar but distinct by design:

* [lazy-init](https://crates.io/crates/lazy-init) – Based on a `LazyTransform<T, U>` which can lazily consume `T` to produce an `U`. Therefore can not support fallible initialization.
* [lazycell](https://crates.io/crates/lazycell) – `AtomicLazyCell` does not support lazy initialization (unlike its non-thread-safe counterpart `LazyCell` using `LazyCell::borrow_with()`).
* [mitochondria](https://crates.io/crates/mitochondria) – Not `Sync`.

This newer crate provides the same functionality:

* [once_cell](https://crates.io/crates/once_cell)

Documentation
-------------

[Read the documentation](https://docs.rs/double-checked-cell)

Changelog
---------

* 1.1.0
  - Fix unsoundness: `DoubleCheckedCell<T>` where `T: !Send` cannot be `Sync`.
* 1.0.1
  - Ignore `unused_unsafe` warning due to `UnsafeCell::into_inner()` no longer
    beeing unsafe.
* 1.0.0
  - Initial release.

License
-------

double-checked-cell is licensed under the [Apache 2.0](http://www.apache.org/licenses/LICENSE-2.0)
and [MIT](http://opensource.org/licenses/MIT) license, at your option.
