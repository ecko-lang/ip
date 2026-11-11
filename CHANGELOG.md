# Changelog

## 0.38.0

**Fixed**

- IPv4 octets with leading zeros (`010.0.0.1`), surrounding whitespace and
  `+`/`-` signs are refused instead of read as decimal.
- IPv6 addresses where `::` stands for no groups (`1:2:3:4:5:6:7:8::`) are
  refused.
- An embedded IPv4 that is not the last 32 bits of an IPv6 address
  (`1.2.3.4::1`, `::1.2.3.4:1`) is refused.
- `is_private` recognises IPv4-mapped IPv6 addresses (`::ffff:10.0.0.1`).
- `in_network` refuses a prefix outside 0-32 or 0-128, a signed or
  zero-padded prefix, a block with two slashes or none, instead of answering
  `true` or `false`.
- A non-string argument fails with a message naming the function.

**Breaking**

- Errors are now kind `ip` with a `reason` (`bad_address`, `bad_cidr`) instead
  of kind `value`.
  Migration: match `get(e, "kind") == "ip"` where you matched `"value"`.
- `is_valid` raises `bug` for a non-string argument instead of returning
  `false`.
  Migration: convert or check the value first, `is_string(s) and ip.is_valid(s)`.
