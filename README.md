# ip

IPv4 and IPv6 address parsing, validation, CIDR membership and private-range
checks, with strict parsing that refuses the ambiguous forms attackers use to
slip past allowlists. Pure computation - no capabilities.

## Install

```bash
ecko get github.com/ecko-lang/ip
```

```ecko
import ip
```

## Usage

```ecko
import ip

print(ip.parse("192.168.1.5"))  # {groups: [192, 168, 1, 5], is_private: true, version: 4}
print(ip.is_valid("::1"))  # true
print(ip.is_valid("010.0.0.1"))  # false
print(ip.version("2001:db8::1"))  # 6
print(ip.in_network("10.0.0.5", "10.0.0.0/24"))  # true
print(ip.is_private("8.8.8.8"))  # false
print(ip.is_private("::ffff:10.0.0.1"))  # true

try {
    ip.in_network("10.0.0.5", "10.0.0.0/33")
} catch (e) {
    print(e.reason)  # bad_cidr
}
```

## API

| function | returns |
|---|---|
| `parse(s)` | `{ version, groups, is_private }`; `groups` is 4 octets (v4) or 8 hextets (v6) as Ints |
| `is_valid(s)` | `true` when `s` is a valid IPv4 or IPv6 address |
| `version(s)` | `4` or `6` |
| `in_network(addr, cidr)` | `true` when `addr` is inside `cidr` (such as `"10.0.0.0/24"`); `false` across IP versions |
| `is_private(s)` | `true` for RFC 1918, loopback, link-local, unique-local and IPv4-mapped private addresses |

## Notes

### Errors

| kind | reason | when |
|---|---|---|
| `ip` | `bad_address` | an address does not parse (`parse`, `version`, `is_private`, `in_network`) |
| `ip` | `bad_cidr` | `in_network`'s block is not `<address>/<prefix>`, or the prefix is outside 0-32 (v4) or 0-128 (v6) |
| `bug` | `bad_argument` | an argument is not a String |

The errors carry the offending text as `address` or `cidr`.

### Strict parsing

An address is refused rather than guessed at:

- IPv4 octets may not have leading zeros. `010.0.0.1` is octal `8.0.0.1` to
  some parsers and decimal to others, and that disagreement is how allowlists
  are bypassed (Python's `ipaddress` refuses it for the same reason).
- No whitespace, `+`/`-` signs, brackets (`[::1]`) or zone ids (`fe80::1%eth0`).
- In IPv6, `::` must stand for at least one zero group, so
  `1:2:3:4:5:6:7:8::` is refused, and an embedded IPv4 may only be the last
  32 bits (`::ffff:1.2.3.4`).
- A CIDR prefix is a plain decimal: `/08`, `/+8` and `/-1` are refused.

### How it works

An address is kept as its groups, so CIDR membership compares the network's
leading `prefix` bits group by group, and IPv6's 128-bit width never has to fit
in one Int.

## Testing

```bash
ecko test
```

## License

[MIT](LICENSE)
