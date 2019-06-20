The Varpad Padding Scheme
=========================

Unlimited padding with an embedded length field.

Varpad is a padding encoding scheme that allows unambiguous detection of the padding length without requiring an external field. It is similar to the scheme described in [PKCS#7](http://tools.ietf.org/html/rfc5652#section-6.3), but doesn't suffer from its limitations (PKCS#7 padding has a length limit of 255).



Features
--------

 * Doesn't require a separate length field (the length is embedded in the padding itself).
 * Padding length can be unambiguously detected by examining the first or last byte of the payload (depending on the ordering of message and padding in the payload).
 * No upper limit on padding length.
 * Easy to eyeball in a packet/memory dump.



Encoding Process
----------------

The count of padding bytes is encoded both at the beginning of the padding data as a [VLQ](https://github.com/kstenerud/vlq/blob/master/vlq-specification.md), and at the end of the padded data as a [reverse VLQ](https://github.com/kstenerud/vlq/blob/master/vlq-specification.md#reverse-vlq). All intervening bytes are set to the same value as the last byte of the normal (not reverse) VLQ encoded value.

    [byte count (normal VLQ)] [padding bytes] [byte count (reverse VLQ)]

Padding is always added, even if the message length is already a multiple of the required padding modulus (and would thus normally not need padding). This ensures that every message will always contain a padding field, the length of which can be found by inspecting the last byte of a (message + padding) payload, or the first byte of a (padding + message) payload. You could of course use an external bit field that determines the presence of the padding field in order to get around this edge case.


### Example 1

For padding counts less than 128, varpad behaves exactly like [PKCS#7](http://tools.ietf.org/html/rfc5652#section-6.3)

| Bytes                | Padding |
| -------------------- | ------- |
| `[01]`               |       1 |
| `[02 02]`            |       2 |
| `[03 03 03]`         |       3 |
| `[04 04 04 04]`      |       4 |
| `[7f` `7f`x125 `7f]` |     127 |


### Example 2

Padding counts greater than 127 look a little different due to the [VLQ](https://github.com/kstenerud/vlq/blob/master/vlq-specification.md) encoding:

| Bytes                      | Padding |
| -------------------------- | ------- |
| `[81 00` `00`x124 `00 81]` |     128 |

The padding byte count 128 (0x80) encodes to the VLQ `[81 00]`, which is placed at the beginning of the padding sequence, and also at the end of the sequence in reversed order. The last byte (0x00) of the VLQ encoded byte count field is used to fill the middle of the padding sequence.


| Bytes                       | Padding |
| --------------------------- | ------- |
| `[92 03` `03`x2303 `03 92]` |    2307 |

The padding byte count 2307 (0x903) encodes to the VLQ `[92 03]`. The last byte (0x03) of the VLQ encoded byte count field is used to fill the middle of the padding sequence.



License
-------

Copyright (c) 2019 Karl Stenerud. All rights reserved.

Distributed under the Creative Commons Attribution License: https://creativecommons.org/licenses/by/4.0/legalcode
License deed: https://creativecommons.org/licenses/by/4.0/
