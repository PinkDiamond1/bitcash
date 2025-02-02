.. _keys:

Keys
====

BitCash assumes the easiest way to reason about BitcoinCash is in the basic unit of a
`private key`_ and, therefore, all functionality (viewing balance, making
transactions, etc.) is wrapped up in this class for convenience. You shouldn't
have to use anything else.

What is a private key?
----------------------

A private key is a derived point on an `elliptic curve`_. BitcoinCash and other
cryptocurrencies use the `SEC`_ defined curve `secp256k1`_ due to its
properties that allow for particularly fast computation. The secret part
of a private key is essentially just a sequence of randomly generated bytes
(32 max); nothing special by itself. The features come when you derive a point
on the curve from that value.

**This value must be kept secret or you risk losing your funds permanently!**

Types
-----

bitcash defines 2 kinds of private keys: :class:`~bitcash.PrivateKey`, aliased as
simply :class:`~bitcash.Key`, and :class:`~bitcash.PrivateKeyTestnet`. Both classes
have the exact same functionality.

.. code-block:: python

    >>> from bitcash import Key, PrivateKey, PrivateKeyTestnet
    >>>
    >>> Key == PrivateKey
    True
    >>>
    >>> dir(Key) == dir(PrivateKeyTestnet)
    True

The only difference is the network operations for :class:`~bitcash.PrivateKeyTestnet`
will use BitcoinCash's test network where coins have no value. As such, the derived
address will also be different.

Creation
--------

Creation of a new private key is as simple as:

.. code-block:: python

    >>> from bitcash import Key
    >>> key = Key()

Public Point
------------

Each unique value applied to the curve will yield a unique point. As this
is a one-way function, there is no known way to derive the private value
from the point.

You can see it like this:

.. code-block:: python

    >>> key.public_point
    Point(x=97142194878787224003202190607135598251247304976930950188466413219499502457410, y=43606604972619611673144670688496329906728122067438546662512577612023859619611)

You will never use this directly.

Public Key
----------

A public key is a public point serialized to bytes. By default all keys will
use the compressed version. This reduces the size of each transaction and thus fees.

Access it like so:

.. code-block:: python

    >>> key.public_key
    b'\x03\xd6\xc4\x88\xab[S\x1d_\xb1H\x12\xbe\x19C\x08\x16\x90~@\x9fv-\x95\xf0w\x87\xfdB\x81\xa3\xd2B'

You will also never use this directly. This value is only used internally to
derive your address and is needed in the construction of every transaction.

Address
-------

All keys possess an :func:`~bitcash.PrivateKey.address` property which is derived from your public key:

.. code-block:: python

    >>> key.address
    'bitcoincash:qqrxvhnn88gmpczyxry254vcsnl6canmkqgt98lpn5'

This is what you share with others to receive payments.

Formats
-------

WIF
^^^

The `wallet import format`_ is the primary way of representing private keys. This
format stores the secret value as well as some metadata such as whether or not the
public key should be compressed.

To import a private key you can pass a key in wallet import format directly to
the initializer:

.. code-block:: python

    >>> key = Key('L3jsepcttyuJK3HKezD4qqRKGtwc8d2d1Nw6vsoPDX9cMcUxqqMv')
    >>> key.address
    'bitcoincash:qqrxvhnn88gmpczyxry254vcsnl6canmkqgt98lpn5'

Export:

.. code-block:: python

    >>> key = Key()
    >>> key.to_wif()
    'KxVhypbvS3hEYPAP3pYuH1LtcfbdEUcugiqg7fNFUUnmEfWVXJV4'

If you don't know what kind of private key your WIF represents, and you don't
want to force the use of a particular class, you can do this:

.. code-block:: python

    >>> from bitcash import wif_to_key
    >>>
    >>> key = wif_to_key('cU6s7jckL3bZUUkb3Q2CD9vNu8F1o58K5R5a3JFtidoccMbhEGKZ')
    >>> print(key)
    <PrivateKeyTestnet: muUFbvTKDEokGTVUjScMhw1QF2rtv5hxCz>

Hex
^^^

Import:

.. code-block:: python

    >>> key = Key.from_hex('c28a9f80738f770d527803a566cf6fc3edf6cea586c4fc4a5223a5ad797e1ac3')
    >>> key.address
    'bitcoincash:qqrxvhnn88gmpczyxry254vcsnl6canmkqgt98lpn5'

Export:

.. code-block:: python

    >>> key = Key()
    >>> key.to_hex()
    '738fc299281ba8d29f54cbc6b064f9b468e064a02fe6807af8367cbb25b20673'

Integer
^^^^^^^

Import:

.. code-block:: python

    >>> key = Key.from_int(87993618360805341115891506172036624893404292644470266399436498750715784469187)
    >>> key.address
    'bitcoincash:qzvsaasdvw6mt9j2rs3gyps673gj86flev4sthhcc0'

Export:

.. code-block:: python

    >>> key = Key()
    >>> key.to_int()
    100038680087491563809217308525458351872289809954309021777847614847412668376286

PEM
^^^

Import:

.. code-block:: python

    >>> key = Key.from_pem(...)
    >>> key.address
    'bitcoincash:qqrxvhnn88gmpczyxry254vcsnl6canmkqgt98lpn5'

Export:

.. code-block:: python

    >>> key = Key()
    >>> key.to_pem()
    b'-----BEGIN PRIVATE KEY-----\nMIGEAgEAMBAGByqGSM49AgEGBSuBBAAKBG0wawIBAQQg93tloWnF8UvDLeK2n0OE\nyf/Si6O73rm33ctZHVhTIBGhRANCAARG3vtgCf5SGfIkwcvuAxNvO/tdy8HnWqS3\nUM+KrWUPpPnHeysZbO6zrG4tN/VBeV2p3whYU6dUhSueOXUPB2Oo\n-----END PRIVATE KEY-----\n'

DER
^^^

Import:

.. code-block:: python

    >>> key = Key.from_der(...)
    >>> key.address
    'bitcoincash:qqrxvhnn88gmpczyxry254vcsnl6canmkqgt98lpn5'

Export:

.. code-block:: python

    >>> key = Key()
    >>> key.to_der()
    b'0\x81\x84\x02\x01\x000\x10\x06\x07*\x86H\xce=\x02\x01\x06\x05+\x81\x04\x00\n\x04m0k\x02\x01\x01\x04 ld\x8f\xd4\xc0\x19\xbd^\xa1\xf7f\xee\x8b9j\x1c\xd3ZX\x89\x1b\x04\x13|e\xe7|g\x84:\xcf\xab\xa1D\x03B\x00\x04\xb6\x1a\x9bQ\x0c?\xe3\xb7\x80\x05,\xcf7\x01{\xf9,"\xb6\xdf\xe5\xbb\x0b+\x9b\xc5\x07@2\xa1\x8a\x01R<\x86\t\x1c\x02\x0fd\x8d\x90\xb5\x99w\xc5\x84(#\xfdr>^\xd3\xb5|\x9d1\xa1\x9c/\x04\xf5\xdd'

.. _private key: https://en.bitcoin.it/wiki/Private_key
.. _elliptic curve: https://en.wikipedia.org/wiki/Elliptic_curve
.. _SEC: https://en.wikipedia.org/wiki/SECG
.. _secp256k1: https://en.bitcoin.it/wiki/Secp256k1
.. _wallet import format: https://en.bitcoin.it/wiki/Private_key#Base58_Wallet_Import_format
