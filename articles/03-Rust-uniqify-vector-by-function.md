Rust - Uniqify vector by function
===

Recently I was looking for a function that can deduplicate a vector in Rust via some custom "deduplication" callback function. There is `Vec::dedup` but if I need some special logic for how to uniqify the vector, there was no way for it.

I was a bit surprised that there is nothing like that in the Rust standard library, but soon there will - there is a Pull Request for adding [`Vec::dedup_by` and `Vec::dedup_by_key`](https://github.com/rust-lang/rust/pull/36743) functionality and it was merged not too long ago. Given the release frequency of Rust, I'm pretty sure it will land very soon in stable, in the meantime I backported the functions:

<script src="https://gist.github.com/maxbeutel/f51ff7d882cda7037da1cadfe20ff59d.js"></script>
