The Varpad Padding Scheme
=========================

Unlimited padding with an embedded length field.

Varpad is a padding encoding scheme that allows unambiguous detection of the padding length without requiring an external field. It is similar to the scheme described in [PKCS#7](http://tools.ietf.org/html/rfc5652#section-6.3), but doesn't suffer from its limitations (PKCS#7 padding has a length limit of 255).

Varpad is part of the [Specifications Project](https://github.com/kstenerud/specifications).


### Features

 * Doesn't require a separate length field (the length is embedded in the padding itself).
 * Padding length can be unambiguously detected by examining the first or last byte of the payload (depending on the ordering of message and padding in the payload).
 * No upper limit on padding length.
 * Easy to eyeball in a packet/memory dump.



Specification
-------------

* [Varpad Specification](varpad-specification.md)



Implementations
---------------

* [Go implementation](https://github.com/kstenerud/go-varpad)



License
-------

Copyright 2019 Karl Stenerud

Specification released under Creative Commons Attribution 4.0 International Public License https://creativecommons.org/licenses/by/4.0/
