# IP - Ecko Std Lib Package

IPv4 / IPv6 address parsing, validation, CIDR membership, and private-range
checks for [Ecko](https://ecko.sh), written in Ecko. Pairs well with the
`validate` package for input rules.

## Install

```bash
ecko get github.com/ecko-lang/ip
```

## Usage

```ecko
import ip

ip.parse("192.168.1.5")
# { version: 4, groups: [192, 168, 1, 5], is_private: true }

ip.is_valid("::1")                          # true
ip.version("2001:db8::1")                   # 6
ip.in_network("10.0.0.5", "10.0.0.0/24")    # true
ip.is_private("8.8.8.8")                    # false
```

## API

| Function | Description |
|---|---|
| `parse(s)` | `{ version, groups, is_private }`, or raises kind-`"value"` if invalid |
| `is_valid(s)` | `Bool` |
| `version(s)` | `4` or `6` |
| `in_network(addr, cidr)` | Is `addr` inside `cidr` (e.g. `"10.0.0.0/24"`)? |
| `is_private(s)` | RFC 1918 / loopback / link-local / unique-local |

An address is stored as its **groups** - four 8-bit octets (v4) or eight 16-bit
hextets (v6). `groups` is a list of integers.

## How it works

CIDR membership compares the network's leading `prefix` bits against the
address, **group by group** - so IPv6's 128-bit width never needs to fit in a
single integer (Ecko's `Int` is `i64`). IPv6 parsing handles `::` compression
and an embedded dotted-quad IPv4 tail (`::ffff:1.2.3.4`).

## Testing

```bash
ecko test tests/
```

## License

MIT - see [LICENSE](LICENSE).
