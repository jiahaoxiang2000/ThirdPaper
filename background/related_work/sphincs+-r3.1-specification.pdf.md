SPHINCS+

Submission to the NIST post-quantum project, v.3.1

Jean-Philippe Aumasson, Daniel J. Bernstein, Ward Beullens,
Christoph Dobraunig, Maria Eichlseder, Scott Fluhrer, Stefan-Lukas Gazdag,
Andreas Hülsing, Panos Kampanakis, Stefan Kölbl, Tanja Lange,
Martin M. Lauridsen, Florian Mendel, Ruben Niederhagen, Christian Rechberger,
Joost Rijneveld, Peter Schwabe, Bas Westerbaan

June 10, 2022

1

Contents

1.

Introduction
1.1. SPHINCS+ vs SPHINCS . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
1.2. Organization . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .

5
6
7

2. Notation

2.1. Data Types . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
2.2. Functions
. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
2.3. Operators . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
2.4.
Integer to Byte Conversion (Function toByte) . . . . . . . . . . . . . . . . . . .
2.5. Strings of Base-w Numbers (Function base_w) . . . . . . . . . . . . . . . . . . .
2.6. Member Functions (Functions set, get) . . . . . . . . . . . . . . . . . . . . . .
2.7. Cryptographic (Hash) Function Families . . . . . . . . . . . . . . . . . . . . . .
. . . . . . . . . . . . .

7
7
7
7
8
8
9
9
2.7.1. Tweakable Hash Functions (Functions T_l, F, H)
9
2.7.2. PRF and Message Digest (Functions PRF, PRF_msg, H_msg) . . . . . . . . 11
2.7.3. Hash Function Address Scheme (Structure of ADRS) . . . . . . . . . . 11

3. WOTS+ One-Time Signatures

14
3.1. WOTS+ Parameters . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 14
3.2. WOTS+ Chaining Function (Function chain) . . . . . . . . . . . . . . . . . . . 15
3.3. WOTS+ Private Key (Function wots_SKgen)
. . . . . . . . . . . . . . . . . . . 15
3.4. WOTS+ Public Key Generation (Function wots_PKgen) . . . . . . . . . . . . . 16
3.5. WOTS+ Signature Generation (Function wots_sign) . . . . . . . . . . . . . . . 16
3.6. WOTS+ Compute Public Key from Signature (Function wots_pkFromSig) . . . 17

4. The SPHINCS+ Hypertree

19
4.1. (Fixed Input-Length) XMSS . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 19
. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 19
4.1.1. XMSS Parameters
4.1.2. XMSS Private Key . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 19
4.1.3. TreeHash (Function treehash) . . . . . . . . . . . . . . . . . . . . . . . 19
. . . . . . . . . . 20
4.1.4. XMSS Public Key Generation (Function xmss_PKgen)
4.1.5. XMSS Signature . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 21
4.1.6. XMSS Signature Generation (Function xmss_sign) . . . . . . . . . . . . 21
4.1.7. XMSS Compute Public Key from Signature (Function xmss_pkFromSig) 22
4.2. HT: The Hypertee . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 23
4.2.1. HT Parameters . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 23
4.2.2. HT Key Generation (Function ht_PKgen)
. . . . . . . . . . . . . . . . . 23
4.2.3. HT Signature . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 24
4.2.4. HT Signature Generation (Function ht_sign) . . . . . . . . . . . . . . . 24
. . . . . . . . . . . . . 25
4.2.5. HT Signature Veriﬁcation (Function ht_verify)

5. FORS: Forest Of Random Subsets

26
. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 26
5.1. FORS Parameters
5.2. FORS Private Key (Function fors_SKgen) . . . . . . . . . . . . . . . . . . . . . 27
5.3. FORS TreeHash (Function fors_treehash) . . . . . . . . . . . . . . . . . . . . 27
5.4. FORS Public Key (Function fors_PKgen) . . . . . . . . . . . . . . . . . . . . . 28

2

5.5. FORS Signature Generation (Function fors_sign) . . . . . . . . . . . . . . . . 29
5.6. FORS Compute Public Key from Signature (Function fors_pkFromSig) . . . . 29

6. SPHINCS+

31
. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 31
6.1. SPHINCS+ Parameters
. . . . . . . . . . . . . . . 32
6.2. SPHINCS+ Key Generation (Function spx_keygen)
6.3. SPHINCS+ Signature
. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 32
6.4. SPHINCS+ Signature Generation (Function spx_sign) . . . . . . . . . . . . . . 33
6.5. SPHINCS+ Signature Veriﬁcation (Function spx_verify) . . . . . . . . . . . . 34

7.

Instantiations
7.1. SPHINCS+ Parameter Sets

35
. . . . . . . . . . . . . . . . . . . . . . . . . . . . . 35
7.1.1.
Inﬂuence of Parameters on Security and Performance . . . . . . . . . . . 36
7.1.2. Proposed Parameter Sets and Security Levels . . . . . . . . . . . . . . . 38
Instantiations of Hash Functions
. . . . . . . . . . . . . . . . . . . . . . . . . . 38
7.2.1. SPHINCS+-SHAKE . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 39
7.2.2. SPHINCS+-SHA2 . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 40
7.2.3. SPHINCS+-Haraka . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 41

7.2.

8. Design rationale

8.1. Changes Made

42
. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 43
8.1.1. Multi-Target Attack Protection . . . . . . . . . . . . . . . . . . . . . . . 43
8.1.2. Tree-less WOTS+ Public Key Compression . . . . . . . . . . . . . . . . 43
8.1.3. FORS . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 44
8.1.4. Veriﬁable Index Selection . . . . . . . . . . . . . . . . . . . . . . . . . . 44
. . . . . . . . . . . . . . . . . . 45
8.1.5. Making Deterministic Signing Optional
. . . . . . . . . . . . . . . 45
8.1.6. SPHINCS+-’simple’ and SPHINCS+-’robust’
8.2. Discarded Changes . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 46

9. Security Evaluation (including estimated security strength and known attacks)

46
9.1. Preliminaries
. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 48
9.2. Security Reduction . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 50
9.3. Security Level / Security Against Generic Attacks . . . . . . . . . . . . . . . . . 50
9.3.1. Distinct-Function Multi-Target Second-Preimage Resistance . . . . . . . 51
9.3.2. Pseudorandomness of Function Families . . . . . . . . . . . . . . . . . . 51
Interleaved Target Subset Resilience . . . . . . . . . . . . . . . . . . . . 51
9.3.3.
9.3.4. Security Level of a Given Parameter Set . . . . . . . . . . . . . . . . . . 52
9.4.
Implementation Security and Side-Channel Protection . . . . . . . . . . . . . . 53
9.5. Security of SPHINCS+-SHAKE . . . . . . . . . . . . . . . . . . . . . . . . . . . 53
9.6. Security of SPHINCS+-SHA2 . . . . . . . . . . . . . . . . . . . . . . . . . . . . 54
9.7. Security of SPHINCS+-Haraka . . . . . . . . . . . . . . . . . . . . . . . . . . . 54

10.Performance

55
10.1. Runtime . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 55
. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 55
10.2. Space

11.Advantages and Limitations

58

3

12.Acknowledgements

A. Parameter-evaluation Sage script

59

62

4

1. Introduction

Hash-based signature schemes were developed as one-time signature schemes in the late 1970s
by Lamport [15] and extended to many-times signatures by Merkle [17]. The security of these
schemes is easy to analyze and relies solely on the properties of the used hash function. How-
ever, Merkle’s tree-based signature scheme required ﬁxing at key-generation time the number
of signatures to be made, keeping this number small for performance. Most importantly,
the system required users to remember a state: some information to remember how many
signatures were already made with the key.

In the 40 years since Lamport’s scheme, many ideas improved the performance, practicality,
and theoretical foundations of hash-based signatures, culminating in XMSS [7], which is by
now – as the ﬁrst post-quantum signature scheme – published as an RFC [8] by the CFRG.
A strong point of these systems is that they need very few security assumptions – the hash
function even need not be collision resistant. The only downside of XMSS is that it is stateful,
which makes it not ﬁt the standard deﬁnition of signature schemes as, e.g., stated in the NIST
call for submissions.

SPHINCS [5] was designed by Bernstein, Hopwood, Hülsing, Lange, Niederhagen, Pa-
pachristodoulou, Schneider, Schwabe, and Wilcox-O’Hearn as a stateless hash-based signature
scheme and was the ﬁrst signature scheme to propose parameters to resist quantum cryptanal-
ysis. SPHINCS uses many components from XMSS but works with larger keys and signatures
to eliminate the state.

This document is about the SPHINCS+ construction. At a high level, SPHINCS+ works
like SPHINCS. The basic idea is to authenticate a huge number of few-time signature (FTS)
key pairs using a so-called hypertree. FTS schemes are signature schemes that allow a key
pair to produce a small number of signatures, e.g., in the order of ten for our parameter sets.
For each new message, a (pseudo)random FTS key pair is chosen to sign the message. The
signature consists then of the FTS signature and the authentication information for that FTS
key pair. The authentication information is roughly a hypertree signature, i.e. a signature
using a certiﬁcation tree of Merkle tree signatures.

More speciﬁcally, a hypertree is a tree of hash-based many-time signatures (MTS). These
many-time signatures allow a key pair to sign a ﬁxed number N of messages – for SPHINCS+
N is a power of 2, for example 256. The MTS key pairs themselves are organized in an N -ary
tree with d layers. On the top layer d − 1 there is a single MTS key pair which is used to sign
the public keys of N MTS key pairs that form layer d − 2. Each of these N MTS key pairs is
used to sign another N MTS public keys forming layer d − 3. This goes on down to the N d−1
key pairs on the bottom layer which are used to sign N FTS public keys, each, leading to a
total number of N d authenticated FTS key pairs. The authentication information for an FTS
key pair consists of the d MTS signatures that build a path from the FTS key pair to the top
MTS tree.

An MTS signature is just a classical Merkle-tree signature in the case of SPHINCS+. It
consists of a one-time signature (OTS) on the given message plus the authentication path in
the binary hash-tree, authenticating the N OTS key pairs of one MTS key pair.

The public key of SPHINCS+ is essentially the public key of the top level MTS which is
just the root node of its binary hash tree and hence, a single hash value. However, actual
SPHINCS+ public keys additionally contain a public seed value of the same length as the root
node. This is due to technical reasons explained in the detailed speciﬁcation below.

The SPHINCS+ secret key is just a single secret seed value. From this, all the OTS and

5

FTS secret keys are generated in a pseudorandom manner. The OTS and FTS secret keys
together fully determine the whole virtual structure of an SPHINCS+ key pair. Again, actual
SPHINCS+ secret keys contain an additional secret value of the same size as the secret seed as
well as a copy of the public key. The additional value is used to key a PRF used in randomized
hashing as detailed in the comparison below.

1.1. SPHINCS+ vs SPHINCS

SPHINCS+ builds on SPHINCS by introducing several improvements:

• Multi-target attack protection: We apply the mitigation techniques from [11] using keyed
hash functions. Each hash function call is keyed with a diﬀerent key and applies diﬀerent
bitmasks. Keys and bitmasks are pseudorandomly generated from an address specifying
the context of the call, and a public seed. For this we introduce the notion of tweakable
hash functions which in addition to the input value take a public seed and an address.

• Tree-less WOTS+ public key compression: The last nodes of the WOTS+ chains are not
compressed using an L-tree but using a single tweakable hash function call. This call
again receives an address and a public seed to key this call and to generate a bitmask
as long as the input.

• FORS: A HORST key pair does not consist anymore of a single monolithic tree. Instead
it consists of k trees of height a The leaves of these trees are the hashes of the 2a secret
key elements. The public key is the tweakable hash of the concatenation of all the root
nodes as for the WOTS+ public key.

A FORS key pair can be used to sign k2abit message digests. The digest is ﬁrst split
into k strings mi of length 2a bits each. Next, every mi is interpreted as an integer
in [0, 2a − 1]. Here mi selects the mi-th secret key element of the i-th tree for the
signature. The signature also contains the authentication paths for all the selected
secret key elements, which means one path of length a per tree. Veriﬁcation uses the
signature to reconstruct the root nodes and compresses them using the tweakable hash.

• Veriﬁable index selection: The message digest is now computed as follows. First, we

deterministically generate randomness

R = PRFmsg(SK.prf, OptRand, M ).

Where OptRand is a n-byte value, per default PK.seed but can be ﬁlled with random
bits e.g. taken from a TRNG to avoid deterministic signing. Then we compute message
digest and index as

(md||idx) = Hmsg(R, PK, M )
where PK = (PK.seed, PK.root) contains the top root node and the public seed.
Hence, we can omit the index in the SPHINCS signature as it would be redundant. This
allows to tighten HORST security.

• SPHINCS+-’robust’ and SPHINCS+-’simple’: The updated, Round 2 submission of
SPHINCS+ adds new, more simple instantiations of the tweakable hash functions simi-
lar to those in the LMS proposal for stateful hash-based signatures [16]. This splits the

6

instantiations into the new ’simple’ instantiations and the established ’robust’ instantia-
tions. The ’simple’ instantiations have the advantage of better speed and the drawback
of a security argument which in its entirety only applies in the random oracle model.
Consequently, the ’robust’ instantiations have a more conservative security argument
but are slower.

1.2. Organization

In this document we give a formal speciﬁcation of the SPHINCS+ construction. We follow
a bottom-up approach to specify SPHINCS+. We start with basic notation. Afterwards we
deﬁne WOTS+, the OTS used in SPHINCS+. Next, we specify XMSS, the MTS used in
SPHINCS+, and how it is used to do HT signatures. Then, we deﬁne FORS, the FTS used,
to ﬁnally specify SPHINCS+. Afterwards we discuss diﬀerent instantiations and explain the
design rationale. Then we present a security analysis, give performance values and conclude
with a discussion of advantages and limitations.

2. Notation

In the following we start deﬁning basic mathematical operations on integers and bit strings.
From that we work our way to more speciﬁc basic methods used later in the speciﬁcation.

2.1. Data Types

Bytes and byte strings are the fundamental data types. A byte is a sequence of eight bits.
The set of bytes is denoted as B. A single byte is denoted as a pair of hexadecimal digits with
a leading "0x". A byte string is an ordered sequence of zero or more bytes and is denoted as
an ordered sequence of hexadecimal characters with a leading "0x". For example, 0xe534f0
is a byte string of length 3. An array of byte strings is an ordered, indexed set starting with
index 0 in which all byte strings have identical length. We assume big-endian representation
for any data types or structures.

2.2. Functions

We deﬁne the following functions:

(cid:100)x(cid:101)(or ceil(x)) : for x a real number returns the smallest integer greater than or equal to x.

(cid:98)x(cid:99)(or ﬂoor(x)) : for x a real number returns the largest integer less than or equal to x.

log(x) : for x a non-negative real number returns the logarithm to base 2 of x. In pseudocode

this function is written as lg.

Trunc(cid:96)(x) : truncates the bit-string x to the ﬁrst (cid:96) bits.

2.3. Operators

When a and b are integers, mathematical operators are deﬁned as follows:

ˆ : ab denotes the result of a raised to the power of b.

7

· : a · b denotes the product of a and b. This operator is sometimes omitted in the absence

of ambiguity, as in usual mathematical notation.

/ : a/b denotes the quotient of a by non-zero b.

% : a % b denotes the non-negative remainder of the integer division of a by b.

+ : a + b denotes the sum of a and b.

− : a − b denotes the diﬀerence of a and b.

++ : a++ denotes incrementing a by 1, i.e., a = a + 1.

<< : a << b denotes a logical left shift of a by b positions, for b being non-negative, i.e.,

a · 2b.

>> : a >> b denotes a logical right shift of a by b positions, for b being non-negative, i.e.

ﬂoor(a/2b).

The standard order of operations is used when evaluating arithmetic expressions.

Arrays are used in the common way, where the i-th element of an array A is denoted A[i].
Byte strings are treated as arrays of bytes where necessary: If X is a byte string, then X[i]
denotes its i-th byte, where X[0] is the leftmost, highest order byte.

If A and B are byte strings of equal length, then:

A AND B denotes the bitwise logical conjunction operation.

A XOR B (or A ⊕ B) denotes the bitwise logical exclusive disjunction operation.

When B is a byte and i is an integer, then B >> i denotes the logical right-shift by i

positions.

If X is an x-byte string and Y a y-byte string, then X||Y denotes the concatenation of X

and Y , with X||Y = X[0] . . . X[x − 1]Y [0] . . . Y [y − 1].

2.4. Integer to Byte Conversion (Function toByte)

For x and y non-negative integers, we deﬁne Z = toByte(x, y) to be the y-byte string con-
taining the binary representation of x in big-endian byte-order.

2.5. Strings of Base-w Numbers (Function base_w)

A byte string can be considered as a string of base w numbers, i.e. integers in the set {0, . . . , w−
1}. The correspondence is deﬁned by the function base_w(X, w, out_len) as follows. Let X be
a len_X- byte string, and w is an element of the set {4, 16, 256}, then base_w(X, w, out_len)
outputs an array of out_len integers between 0 and w − 1 (Figure 1). The length out_len is
REQUIRED to be less than or equal to 8 ∗ len_X/ log(w).

# Input: len_X-byte string X, int w, output length out_len
# Output: out_len int array basew

base_w(X, w, out_len) {

8

int in = 0;
int out = 0;
unsigned int total = 0;
int bits = 0;
int consumed;

for ( consumed = 0; consumed < out_len; consumed++ ) {

if ( bits == 0 ) {
total = X[in];
in++;
bits += 8;

}
bits -= lg(w);
basew[out] = (total >> bits) AND (w - 1);
out++;

}
return basew;

}

Algorithm 1: base_w – Computing the base-w representation

2.6. Member Functions (Functions set, get)

To simplify algorithm descriptions, we assume the existence of member functions. If a complex
data structure like a public key P K contains a variable X then PK.getX() returns the value
of X for this public key. Accordingly, PK.setX(Y) sets variable X in P K to the value held by
Y . Since camelCase is used for member function names, a value z may be referred to as Z in
the function name, e.g. getZ.

2.7. Cryptographic (Hash) Function Families

SPHINCS+ makes use of several diﬀerent function families with cryptographic properties.
Every SPHINCS+ instantiation MUST describe how to implement each of the following func-
tions. For the main instantiations given in this document, this will be done using a single
(hash) function, i.e., SHA2-256 or SHAKE-128. Speciﬁc instantiations are given in Section 7.
SPHINCS+ applies the multi-target mitigation technique from [11], independently keying
and randomizing each hash function call in the original SPHINCS. The implementation of this
randomization and keying diﬀers for diﬀerent instantiations as diﬀerent function families (e.g.,
SHA2 or SHAKE) have diﬀerent properties. Hence, we introduce tweakable hash functions
as a layer of abstraction. All algorithms in this speciﬁcation use tweakable hash functions in
place of traditional hash functions. Later, in Section 7, we describe how to implement the
tweakable hash functions.

In addition to several tweakable hash functions, SPHINCS+ makes use of two PRFs and a
keyed hash function. Input and output length are given in terms of the security parameter n
and the message digest length m, both to be deﬁned more precisely in the coming sections.

2.7.1. Tweakable Hash Functions (Functions T_l, F, H)

A tweakable hash function takes a public seed PK.seed and context information in form of
an address ADRS in addition to the message input. This allows to make the hash function

9

Figure 1: For example, if X is the (big-endian) byte string 0x1234, then base_w(X, 16, 4)

returns the array a = {1, 2, 3, 4}.

X (represented as bits)
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
| 0| 0| 0| 1| 0| 0| 1| 0| 0| 0| 1| 1| 0| 1| 0| 0|
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|

X[1]

X[0]

X (represented as base 16 numbers)
+-----------+-----------+-----------+-----------+
|
|
|
+-----------+-----------+-----------+-----------+

4

3

|

1

|

2

base_w(X, 16, 4)
+-----------+-----------+-----------+-----------+
|
|
|
+-----------+-----------+-----------+-----------+

3

|

4

1

|

2

a[0]

a[1]

a[2]

a[3]

base_w(X, 16, 3)

1

+-----------+-----------+-----------+
|
|
2
+-----------+-----------+-----------+
a[1]

a[2]

a[0]

|

3

|

base_w(X, 16, 2)

+-----------+-----------+
|
|
|
+-----------+-----------+

1

2

a[0]

a[1]

calls for each key pair and position in the virtual tree structure of SPHINCS+ independent
from each other. The addressing scheme will be described in Section 2.7.3.

The schemes described in this speciﬁcation build upon several instantiations of tweakable

hash functions of the form

T(cid:96) : Bn × B32 × B(cid:96)n → Bn,
md ← T(cid:96)(PK.seed, ADRS, M )

mapping and (cid:96)n-byte message M to an n-byte hash value md using an n-byte seed PK.seed
and a 32-byte address ADRS. The function T(cid:96) is denoted by T_l in pseudocode.

There are two special cases which we rename for consistency with previous descriptions of

10

hash-based signature schemes:

F : Bn × B32 × Bn → Bn,
F def= T1
H : Bn × B32 × B2n → Bn
H def= T2

2.7.2. PRF and Message Digest (Functions PRF, PRF_msg, H_msg)

SPHINCS+ makes use of a pseudorandom function PRF for pseudorandom key generation:

PRF : Bn × B32 → Bn.

In addition, SPHINCS+ uses a pseudorandom function PRFmsg to generate randomness for
the message compression:

PRFmsg : Bn × Bn × B∗ → Bn.
To compress the message to be signed, SPHINCS+ uses an additional keyed hash function

Hmsg that can process arbitrary length messages:

Hmsg : Bn × Bn × Bn × B∗ → Bm.

2.7.3. Hash Function Address Scheme (Structure of ADRS)

An address ADRS is a 32-byte value that follows a deﬁned structure. In addition, it comes
with set methods to manipulate the address. We explain the generation of addresses in the
following sections where they are used. Essentially, all functions have to keep track of the
current context, updating the addresses after each hash call.

There are ﬁve diﬀerent types of addresses for the diﬀerent use cases. One type is used for the
hashes in WOTS+ schemes, one is used for compression of the WOTS+ public key, the third
is used for hashes within the main Merkle tree construction, another is used for the hashes in
the Merkle tree in FORS, and the last is used for the compression of the tree roots of FORS.
These types largely share a common format. We describe them in more detail, below.

The structure of an address complies with word borders, with a word being 32 bits long in
this context. Only the tree address (i.e. the index of a speciﬁc subtree in the main tree) is too
long to ﬁt a single word: for this, we reserve three words. An address is structured as follows.
It always starts with a layer address of one word in the most signiﬁcant bits, followed by a tree
address of three words. These addresses describe the position of a tree within the hypertree.
The layer address describes the height of a tree within the hypertree starting from height zero
for trees on the bottom layer. The tree address describes the position of a tree within a layer
of a multi-tree starting with index zero for the leftmost tree. The next word deﬁnes the type
It is set to 0 for a WOTS+ hash address, to 1 for the compression of the
of the address.
WOTS+ public key, to 2 for a hash tree address, to 3 for a FORS address, and to 4 for the
compression of FORS tree roots.

We ﬁrst describe the WOTS+ address (Figure 2). In this case, the type word is followed
by the key pair address that encodes the index of the WOTS+ key pair within the speciﬁed

11

tree. The next word encodes the chain address (i.e. the index of the chain within WOTS+),
followed by a word that encodes the address of the hash function call within the chain. Note
that for the generation of the secret keys based on SK.seed a diﬀerent type of address is used
(see below).

layer address

tree address

type = 0

key pair address

chain address

hash address

Figure 2: WOTS+ hash address.

The second type (Figure 3) is used to compress the WOTS+ public keys. The type word
is set to 1. Similar to the address used within WOTS+, the next word encodes the key pair
address. The remaining two words are not needed, and thus remain zero. We zero pad the
address to the constant length of 32 bytes.

layer address

tree address

type = 1

key pair address

padding = 0

Figure 3: WOTS+ public key compression address.

The third type (Figure 4) addresses the hash functions in the main tree. In this case the
type word is set to 2, followed by a zero padding of one word. The next word encodes the
height of the tree node that is being computed, followed by a word that encodes the index of
this node at that height.

layer address

tree address

type = 2

padding = 0

tree height

tree index

Figure 4: hash tree address.

The next type (Figure 5) is of a similar format, and is used to describe the hash functions
in the FORS tree. The type word is set to 3. The key pair address is used to signify which
FORS key pair is used, identical to the key pair address in the WOTS+ hash addresses. Its
value is the same as that of the WOTS+ key pair that is used to authenticate it, i.e. its index
as a leaf in the speciﬁed tree. The tree height and tree index ﬁelds are used to address the
hashes within the FORS tree. This is done like for the above-mentioned hashes in the main
tree, with the additional consideration that the tree indices are counted continuously across
the diﬀerent FORS trees. To generate the leaf nodes from SK.seed a diﬀerent typ of address
is used (see below).

12

layer address

tree address

type = 3

key pair address

tree height

tree index

Figure 5: FORS tree address.

The next type (Figure 6) is used to compress the tree roots of the FORS trees. The type
word is set to 4. Like the WOTS+ public key compression address, it contains only the address
of the FORS key pair, but is padded to the full length.

layer address

tree address

type = 4

key pair address

padding = 0

Figure 6: FORS tree roots compression address.

The ﬁnal two types are used for secret key value generation in WOTS+ and FORS. A
WOTS+ key generation address (Figure 7) is the same as a WOTS+ hash address with two
diﬀerences. First, the type word is set to 5. Second, the hash address word is constantly set
to 0. When generating the secret key value for a given chain, the remaining words have to be
set the same way as for the WOTS+ hash addresses used for this chain.

layer address

tree address

type = 5

key pair address

chain address

hash address = 0

Figure 7: WOTS+ key generation address.

Similarly, the FORS key generation type (Figure 8) is the same as the FORS tree address
type, except that the type word is set to 6, and the tree height word is set to 0. As for the
WOTS+ key generation address, the remaining words have to be set as for the FORS tree
address used when processing the generated value.

All ﬁelds within these addresses encode unsigned integers. When describing the generation
of addresses we use set methods that take positive integers and set the bits of a ﬁeld to the
binary representation of that integer, in big-endian notation. Throughout this document, we
adhere to the convention of assuming that changing the type word of an address (indicated
by the use of the setType() method) initializes the subsequent three words to zero.

In order to make keeping track of the types easier throughout the pseudo-code in the rest of
this document, we refer to them respectively using the constants WOTS_HASH, WOTS_PK, TREE,
FORS_TREE, FORS_ROOTS, WOTS_PRF, and FORS_PRF.

13

layer address

tree address

type = 6

key pair address

tree height = 0

tree index

Figure 8: FORS key generation address.

3. WOTS+ One-Time Signatures

This section describes the WOTS+ scheme, in a version similar to [9]. WOTS+ is a OTS
scheme; while a private key can be used to sign any message, each private key MUST NOT
be used to sign more than a single message. In particular, if a private key is used to sign two
diﬀerent messages, the scheme becomes insecure.

The description given here is tailored to the use inside of SPHINCS+.

It assumes that
the scheme is used as a subroutine inside a higher order scheme and is not suﬃcient for a
standalone implementation of WOTS+. The section starts with an explanation of parame-
ters. Afterwards, the so-called chaining function, which forms the main building block of the
WOTS+ scheme, is explained. A description of the algorithms for key generation and sign-
ing follows. Finally, we give an algorithm to compute a WOTS+ public key from a WOTS+
signature. This will be used as a subroutine in SPHINCS+ signature veriﬁcation.

3.1. WOTS+ Parameters

WOTS+ uses the parameters n and w; they both take positive integer values. These parame-
ters are summarized as follows:

• n: the security parameter; it is the message length as well as the length of a private key,

public key, or signature element in bytes.

• w: the Winternitz parameter; it is an element of the set {4, 16, 256}.

These parameters are used to compute values len, len1 and len2:

• len: the number of n-byte-string elements in a WOTS+ private key, public key, and

signature. It is computed as len = len1 + len2, with

len1 =

(cid:24) 8n

log(w)

(cid:25)

, len2 =

(cid:22) log (len1(w − 1))
log(w)

(cid:23)

+ 1

The security parameter n is the same as the security parameter n for SPHINCS+. The value
of n determines the in- and output length of the tweakable hash function used for WOTS+.
The value of n also determines the length of messages that can be processed by the WOTS+
signing algorithm. The parameter w can be chosen from the set {4, 16, 256}. A larger value of
w results in shorter signatures but slower operations; it has no eﬀect on security. Choices of
w are limited to the values 4, 16, and 256 since these values yield optimal trade-oﬀs and easy
implementation. WOTS+ parameters are implicitly included in algorithm inputs as needed.

14

3.2. WOTS+ Chaining Function (Function chain)
The chaining function (Algorithm 2) computes an iteration of F on an n-byte input using
a WOTS+ hash address ADRS and a public seed PK.seed. The address ADRS MUST
have the ﬁrst seven 32-bit words set to encode the address of this chain. In each iteration,
the address is updated to encode the current position in the chain before ADRS is used to
process the input by F.

In the following, ADRS is a 32-byte WOTS+ hash address as speciﬁed in Section 2.7.3 and
PK.seed is a n-byte string. The chaining function takes as input an n-byte string X;, a start
index i, a number of steps s, as well as ADRS and PK.seed. The chaining function returns
as output the value obtained by iterating F for s times on input X.

#Input: Input string X, start index i, number of steps s, public seed PK.seed,

address ADRS

#Output: value of F iterated s times on X

chain(X, i, s, PK.seed, ADRS) {

if ( s == 0 ) {

return X;

}
if ( (i + s) > (w - 1) ) {

return NULL;

}
byte[n] tmp = chain(X, i, s - 1, PK.seed, ADRS);

ADRS.setHashAddress(i + s - 1);
tmp = F(PK.seed, ADRS, tmp);
return tmp;

}

Algorithm 2: chain – Chaining function used in WOTS+.

3.3. WOTS+ Private Key (Function wots_SKgen)
The WOTS+ private key, denoted by sk (s for secret), is a length len array of n-byte strings.
This private key MUST NOT be used to sign more than one message. This private key is
only implicitly used. Therefore, the following is just to support a better understanding of
the following algorithms. Each n-byte string in the WOTS+ private key is derived from a
secret seed SK.seed which is part of the SPHINCS+ secret key and a WOTS+ key generation
address skADRS using PRF. The same secret seed is used to generate all secret key values
within SPHINCS+. The address used to generate the i-th n-byte string of sk MUST encode
the position of the i-th hash chain of this WOTS+ instance within the SPHINCS+ structure.
The following pseudocode (Algorithm 3) describes an algorithm to generate a WOTS+

private key.

#Input: secret seed SK.seed, address ADRS
#Output: WOTS+ private key sk

wots_SKgen(SK.seed, ADRS) {

skADRS = ADRS; // copy address to create key generation address
skADRS.setType(WOTS_PRF);
skADRS.setKeyPairAddress(ADRS.getKeyPairAddress());
for ( i = 0; i < len; i++ ) {
skADRS.setChainAddress(i);

15

skADRS.setHashAddress(0);
sk[i] = PRF(SK.seed, skADRS);

}
return sk;

}

Algorithm 3: wots_SKgen – Generating a WOTS+ private key.

3.4. WOTS+ Public Key Generation (Function wots_PKgen)
A WOTS+ key pair deﬁnes a virtual structure that consists of len hash chains of length w.
Each of the len stings of n-bytes in the private key deﬁnes the start node for one hash chain.
The public key is the tweakable hash of the end nodes of these hash chains. To compute the
hash chains, the chaining function (Algorithm 2) is used. A WOTS+ hash address ADRS
and a seed PK.seed have to be provided by the calling algorithm as well as a secret seed
SK.seed. The address ADRS MUST encode the address of the WOTS+ key pair within
the SPHINCS+ structure. Hence, a WOTS+ algorithm MUST NOT manipulate any parts of
ADRS other than the last three 32-bit words. Note that the PK.seed used here is public
information also available to a veriﬁer. The following pseudocode (Algorithm 4) describes an
algorithm for generating the public key pk.

#Input: secret seed SK.seed, address ADRS, public seed PK.seed
#Output: WOTS+ public key pk

wots_PKgen(SK.seed, PK.seed, ADRS) {

wotspkADRS = ADRS; // copy address to create OTS public key address
skADRS = ADRS; // copy address to create key generation address
skADRS.setType(WOTS_PRF);
skADRS.setKeyPairAddress(ADRS.getKeyPairAddress());
for ( i = 0; i < len; i++ ) {
skADRS.setChainAddress(i);
skADRS.setHashAddress(0);
sk[i] = PRF(SK.seed, skADRS);
ADRS.setChainAddress(i);
ADRS.setHashAddress(0);
tmp[i] = chain(sk[i], 0, w - 1, PK.seed, ADRS);

}
wotspkADRS.setType(WOTS_PK);
wotspkADRS.setKeyPairAddress(ADRS.getKeyPairAddress());
pk = T_len(PK.seed, wotspkADRS, tmp);
return pk;

}

Algorithm 4: wots_PKgen – Generating a WOTS+ public key.

3.5. WOTS+ Signature Generation (Function wots_sign)
A WOTS+ signature is a length len array of n-byte strings. The WOTS+ signature is gener-
ated by mapping a message M to len integers between 0 and w − 1. To this end, the message
is transformed into len1 base-w numbers using the base_w function deﬁned in Section 2.5.
Next, a checksum over M is computed and appended to the transformed message as len2
base-w numbers using the base_w function. Note that the checksum may reach a maximum
integer value of len1 · (w − 1) and therefore depends on the parameters n and w. For the

16

parameter sets given in Section 7, a 32-bit unsigned integer is suﬃcient to hold the check-
sum. If other parameter sets are used, the size of the variable holding the integer value of the
checksum MUST be suﬃciently large. Each of the base-w integers is used to select a node
from a diﬀerent hash chain. The signature is formed by concatenating the selected nodes. A
WOTS+ hash address ADRS, a public seed PK.seed, and a secret seed SK.seed have to be
provided by the calling algorithm. The address will encode the address of the WOTS+ key pair
within a greater structure. Hence, a WOTS+ algorithm MUST NOT manipulate any parts
of ADRS other than the last three 32-bit words. Note that the PK.seed used here is public
information also available to a veriﬁer while the secret seed SK.seed is private information.
The pseudocode for generating a WOTS+ signature sig is shown below (Algorithm 5).
#Input: Message M, secret seed SK.seed, public seed PK.seed, address ADRS
#Output: WOTS+ signature sig

wots_sign(M, SK.seed, PK.seed, ADRS) {

csum = 0;

// convert message to base w
msg = base_w(M, w, len_1);

// compute checksum
for ( i = 0; i < len_1; i++ ) {
csum = csum + w - 1 - msg[i];

}

// convert csum to base w
if( (lg(w) % 8) != 0) {

csum = csum << ( 8 - ( ( len_2 * lg(w) ) % 8 ));

}
len_2_bytes = ceil( ( len_2 * lg(w) ) / 8 );
msg = msg || base_w(toByte(csum, len_2_bytes), w, len_2);

skADRS = ADRS; // copy address to create key generation address
skADRS.setType(WOTS_PRF);
skADRS.setKeyPairAddress(ADRS.getKeyPairAddress());
for ( i = 0; i < len; i++ ) {
skADRS.setChainAddress(i);
skADRS.setHashAddress(0);
sk = PRF(SK.seed, skADRS);
ADRS.setChainAddress(i);
ADRS.setHashAddress(0);
sig[i] = chain(sk, 0, msg[i], PK.seed, ADRS);

}
return sig;

}

Algorithm 5: wots_sign – Generating a WOTS+ signature on a message M .

The data format for a signature is given in Figure 9.

3.6. WOTS+ Compute Public Key from Signature (Function wots_pkFromSig)
SPHINCS+ uses implicit signature veriﬁcation for WOTS+.
In order to verify a WOTS+
signature sig on a message M, the veriﬁer computes a WOTS+ public key value from the
signature. This can be done by “completing” the chain computations starting from the signa-
ture values, using the base-w values of the message hash and its checksum. This step, called

17

sig

ots[0]

. . .

n bytes

sig

ots[len − 1]

n bytes

Figure 9: WOTS+ Signature data format.

wots_pkFromSig, is described below in Algorithm 6. The result of wots_pkFromSig then has
to be veriﬁed. In a standalone version, this would be done by simple comparison. When used
in SPHINCS+ the output value is veriﬁed by using it to compute a SPHINCS+ public key.

A WOTS+ hash address ADRS and a public seed PK.seed have to be provided by the
calling algorithm. The address will encode the address of the WOTS+ key pair within the
SPHINCS+ structure. Hence, a WOTS+ algorithm MUST NOT manipulate any parts of
ADRS other than the last three 32-bit words. Note that the PK.seed used here is public
information also available to a veriﬁer.

#Input: Message M, WOTS+ signature sig, address ADRS, public seed PK.seed
#Output: WOTS+ public key pk_sig derived from sig

wots_pkFromSig(sig, M, PK.seed, ADRS) {

csum = 0;
wotspkADRS = ADRS;

// convert message to base w
msg = base_w(M, w, len_1);

// compute checksum
for ( i = 0; i < len_1; i++ ) {
csum = csum + w - 1 - msg[i];

}

// convert csum to base w
csum = csum << ( 8 - ( ( len_2 * lg(w) ) % 8 ));
len_2_bytes = ceil( ( len_2 * lg(w) ) / 8 );
msg = msg || base_w(toByte(csum, len_2_bytes), w, len_2);
for ( i = 0; i < len; i++ ) {
ADRS.setChainAddress(i);
tmp[i] = chain(sig[i], msg[i], w - 1 - msg[i], PK.seed, ADRS);

}

wotspkADRS.setType(WOTS_PK);
wotspkADRS.setKeyPairAddress(ADRS.getKeyPairAddress());
pk_sig = T_len(PK.seed, wotspkADRS, tmp);
return pk_sig;

}

Algorithm 6: wots_pkFromSig – Computing a WOTS+ public key from a message and its

signature.

18

4. The SPHINCS+ Hypertree

In this section, we explain how the SPHINCS+ hypertree is built. We ﬁrst explain how
WOTS+ gets combined with a binary hash tree, leading to a ﬁxed input-length version of the
eXtended Merkle Signature Scheme (XMSS). Afterwards, we explain how to go to a hypertree
from there. The hypertree might be viewed as a ﬁxed input-length version of multi-tree XMSS
(XMSSM T ).

4.1. (Fixed Input-Length) XMSS

XMSS is a method for signing a potentially large but ﬁxed number of messages. It is based
It authenticates 2h(cid:48) WOTS+ public keys using a binary
on the Merkle signature scheme.
tree of height h(cid:48). Hence, an XMSS key pair for height h(cid:48) can be used to sign 2h(cid:48) diﬀerent
messages. Each node in the binary tree is an n-byte value which is the tweakable hash of the
concatenation of its two child nodes. The leaves are the WOTS+ public keys. The XMSS
public key is the root node of the tree. In SPHINCS+, the XMSS secret key is the single secret
seed that is used to generate all WOTS+ secret keys.

An XMSS signature in the context of SPHINCS+ consists of the WOTS+ signature on the
message and the so-called authentication path. The latter is a vector of tree nodes that allow
a veriﬁer to compute a value for the root of the tree starting from a WOTS+ signature. A
veriﬁer computes the root value and veriﬁes its correctness. A standalone XMSS signature
also contains the index of the used WOTS+ key pair. In the context of SPHINCS+ this is not
necessary as the SPHINCS+ signature allows to compute the index for each XMSS signature
contained.

4.1.1. XMSS Parameters

XMSS has the following parameters:

h(cid:48) : the height (number of levels - 1) of the tree.

n : the length in bytes of messages as well as of each node.

w : the Winternitz parameter as deﬁned for WOTS+ in the previous Section.

There are 2h(cid:48) leaves in the tree. XMSS signatures are denoted by SIGXMSS (SIG_XMSS in

pseudocode). WOTS+ signatures are denoted by sig.

XMSS parameters are implicitly included in algorithm inputs as needed.

4.1.2. XMSS Private Key

In the context of SPHINCS+, an XMSS private key is the single secret seed SK.seed contained
in the SPHINCS+ secret key.
It is used to generate the WOTS+ secret keys within the
structure of an XMSS key pair as described in Section 3.

4.1.3. TreeHash (Function treehash)

For the computation of the internal n-byte nodes of a Merkle tree, the subroutine treehash
(Algorithm 7) accepts a secret seed SK.seed, a public seed PK.seed, an unsigned integer s
(the start index), an unsigned integer z (the target node height), and an address ADRS that

19

encodes the address of the containing tree. For the height of a node within a tree, counting
starts with the leaves at height zero. The treehash algorithm returns the root node of a tree
of height z with the leftmost leaf being the WOTS+ pk at index s. It is REQUIRED that
s % 2z = 0, i.e. that the leaf at index s is a leftmost leaf of a sub-tree of height z. Otherwise
the algorithm fails as it would compute non-existent nodes. The treehash algorithm described
here uses a stack holding up to (z − 1) nodes, with the usual stack functions push() and pop().
We furthermore assume that the height of a node (an unsigned integer) is stored alongside a
node’s value (an n-byte string) on the stack.

# Input: Secret seed SK.seed, start index s, target node height z, public seed

PK.seed, address ADRS

# Output: n-byte root node - top node on Stack

treehash(SK.seed, s, z, PK.seed, ADRS) {

if( s % (1 << z) != 0 ) return -1;
for ( i = 0; i < 2^z; i++ ) {
ADRS.setType(WOTS_HASH);
ADRS.setKeyPairAddress(s + i);
node = wots_PKgen(SK.seed, PK.seed, ADRS);
ADRS.setType(TREE);
ADRS.setTreeHeight(1);
ADRS.setTreeIndex(s + i);
while ( Top node on Stack has same height as node ) {

ADRS.setTreeIndex((ADRS.getTreeIndex() - 1) / 2);
node = H(PK.seed, ADRS, (Stack.pop() || node));
ADRS.setTreeHeight(ADRS.getTreeHeight() + 1);

}
Stack.push(node);

}
return Stack.pop();

}

Algorithm 7: treehash – The TreeHash algorithm.

4.1.4. XMSS Public Key Generation (Function xmss_PKgen)

The XMSS public key is computed as described in xmss_PKgen (Algorithm 10). In the context
of SPHINCS+ the XMSS public key PK is the root of the binary hash tree. The root is
computed using treehash. The public key generation takes a secret seed SK.seed, a public
seed PK.seed, and an address ADRS. The latter encodes the position of this XMSS instance
within the SPHINCS+ structure.

# Input: Secret seed SK.seed, public seed PK.seed, address ADRS
# Output: XMSS public key PK

xmss_PKgen(SK.seed, PK.seed, ADRS) {

pk = treehash(SK.seed, 0, h’, PK.seed, ADRS)
return pk;

}

Algorithm 8: xmss_PKgen – Generating an XMSS public key.

20

4.1.5. XMSS Signature

An XMSS signature is a ((len + h(cid:48)) ∗ n)-byte string consisting of

• a WOTS+ signature sig taking len · n bytes,

• the authentication path AUTH for the leaf associated with the used WOTS+ key pair

taking h(cid:48) · n bytes.

The authentication path is an array of h(cid:48) n-byte strings.

It contains the siblings of the
nodes in on the path from the used leaf to the root. It does not contain the nodes on the path
itself. These nodes in AUTH are needed by a veriﬁer to compute a root node for the tree
from a WOTS+ public key. A node N is addressed by its position in the tree. N (x, y) denotes
the yth node on level x with y = 0 being the leftmost node on a level. The leaves are on level
0, the root is on level h(cid:48). An authentication path contains exactly one node on every layer
0 ≤ x ≤ (h(cid:48) − 1). For the ith WOTS+ key pair, counting from zero, the jth authentication
path node is

AUTH[j] = N

j, (cid:98)

(cid:18)

(cid:19)
i
2j (cid:99) ⊕ 1

The computation of the authentication path is discussed in Section 4.1.6.
The data format for a signature is given in Figure 10.

sig (len · n bytes)

AUTH [0] (n bytes)

...

AUTH [h-1] (n bytes)

Figure 10: XMSS Signature

4.1.6. XMSS Signature Generation (Function xmss_sign)

To compute the XMSS signature of a message M in the context of SPHINCS+, the secret seed
SK.seed, the public seed PK.seed, the index idx of the WOTS+ key pair to be used, and the
address ADRS of the XMSS instance are needed. First, a WOTS+ signature of the message
digest is computed using the WOTS+ instance at index idx. Next, the authentication path is
computed.

The node values of the authentication path MAY be computed in any way. The least
memory-intensive method is to compute all nodes using the treehash algorithm (Algorithm 7).
This is described here. Note that the details of how this step is implemented are not relevant
to interoperability; it is not necessary to know any of these details in order to perform the
signature veriﬁcation operation.

21

# Input: n-byte message M, secret seed SK.seed, index idx, public seed PK.seed,

address ADRS

# Output: XMSS signature SIG_XMSS = (sig || AUTH)

xmss_sign(M, SK.seed, idx, PK.seed, ADRS)

// build authentication path
for ( j = 0; j < h’; j++ ) {

k = floor(idx / (2^j)) XOR 1;
AUTH[j] = treehash(SK.seed, k * 2^j, j, PK.seed, ADRS);

}

ADRS.setType(WOTS_HASH);
ADRS.setKeyPairAddress(idx);
sig = wots_sign(M, SK.seed, PK.seed, ADRS);
SIG_XMSS = sig || AUTH;
return SIG_XMSS;

}

Algorithm 9: xmss_sign – Generating an XMSS signature.

4.1.7. XMSS Compute Public Key from Signature (Function xmss_pkFromSig)

SPHINCS+ makes use of implicit signature veriﬁcation of XMSS signatures. An XMSS signa-
ture is used to compute a candidate XMSS public key, i.e., the root of the tree. This is used
in further computations (signature of the tree above) and implicitly veriﬁed by the outcome
of that computation. Hence, this speciﬁcation does not contain an xmss_verify method but
the method xmss_pkFromSig.

The method xmss_pkFromSig takes an n-byte message M , an XMSS signature SIGXMSS,
a signature index idx, a public seed PK.seed, and an address ADRS. The latter encodes
the position of the current XMSS instance within the virtual structure of the SPHINCS+ key
pair. First, wots_pkFromSig is used to compute a candidate WOTS+ public key. This in turn
is used together with the authentication path to compute a root node which is then returned.
The algorithm xmss_pkFromSig is given as Algorithm 10.

# Input: index idx, XMSS signature SIG_XMSS = (sig || AUTH), n-byte message M,

public seed PK.seed, address ADRS

# Output: n-byte root value node[0]

xmss_pkFromSig(idx, SIG_XMSS, M, PK.seed, ADRS){

// compute WOTS+ pk from WOTS+ sig
ADRS.setType(WOTS_HASH);
ADRS.setKeyPairAddress(idx);
sig = SIG_XMSS.getWOTSSig();
AUTH = SIG_XMSS.getXMSSAUTH();
node[0] = wots_pkFromSig(sig, M, PK.seed, ADRS);

// compute root from WOTS+ pk and AUTH
ADRS.setType(TREE);
ADRS.setTreeIndex(idx);
for ( k = 0; k < h’; k++ ) {
ADRS.setTreeHeight(k+1);
if ( (floor(idx / (2^k)) % 2) == 0 ) {

22

ADRS.setTreeIndex(ADRS.getTreeIndex() / 2);
node[1] = H(PK.seed, ADRS, (node[0] || AUTH[k]));

} else {

ADRS.setTreeIndex((ADRS.getTreeIndex() - 1) / 2);
node[1] = H(PK.seed, ADRS, (AUTH[k] || node[0]));

}
node[0] = node[1];

}
return node[0];

}

Algorithm 10: xmss_pkFromSig – Computing an XMSS public key from an XMSS signature.

4.2. HT: The Hypertee

The SPHINCS+ hypertree HT is a variant of XMSSM T . It is essentially a certiﬁcation tree
of XMSS instances. A HT is a tree of several layers of XMSS trees. The trees on top and
intermediate layers are used to sign the public keys, i.e., the root nodes, of the XMSS trees on
the respective next layer below. Trees on the lowest layer are used to sign the actual messages,
which are FORS public keys in SPHINCS+. All XMSS trees in HT have equal height.

Consider a HT of total height h that has d layers of XMSS trees of height h(cid:48) = h/d. Then
layer d − 1 contains one XMSS tree, layer d − 2 contains 2h(cid:48) XMSS trees, and so on. Finally,
layer 0 contains 2h−h(cid:48) XMSS trees.

4.2.1. HT Parameters

In addition to all XMSS parameters, a HT requires the hypertree height h and the number of
tree layers d, speciﬁed as an integer value that divides h without remainder. The same tree
height h(cid:48) = h/d and the same Winternitz parameter w are used for all tree layers.

4.2.2. HT Key Generation (Function ht_PKgen)

The HT private key is the secret seed SK.seed which is used to generate all the WOTS+
private keys within the virtual structure spanned by the HT.

The HT public key is the public key (root node) of the single XMSS tree on the top layer.
Its computation is explained below. The public key generation takes as input a private and a
public seed.

# Input: Private seed SK.seed, public seed PK.seed
# Output: HT public key PK_HT

ht_PKgen(SK.seed, PK.seed){

ADRS = toByte(0, 32);
ADRS.setLayerAddress(d-1);
ADRS.setTreeAddress(0);
root = xmss_PKgen(SK.seed, PK.seed, ADRS);
return root;

}

Algorithm 11: ht_PKgen – Generating an HT public key.

23

4.2.3. HT Signature

A HT signature SIGHT is a byte string of length (h + d ∗ len) ∗ n. It consists of d XMSS
signatures (of (h/d + len) ∗ n bytes each).

The data format for a signature is given in Figure 11

XMSS signature SIGXMSS (layer 0) ((h/d + len) · n bytes)

XMSS signature SIGXMSS (layer 1) ((h/d + len) · n bytes)

...

XMSS signature SIGXMSS (layer d − 1) ((h/d + len) · n bytes)

Figure 11: HT signature

4.2.4. HT Signature Generation (Function ht_sign)

To compute a HT signature SIGHT of a message M using, ht_sign (Algorithm 12) described
below uses xmss_sign as deﬁned in Section 4.1.6. The algorithm ht_sign takes as input a
message M , a private seed SK.seed, a public seed PK.seed, and an index idx. The index
identiﬁes the leaf of the hypertree to be used to sign the message. The HT signature then
consists of a stack of XMSS signatures using the XMSS trees on the path from the leaf with
index idx to the top tree. Note that idx is passed as two separate arguments, split into an index
to address the speciﬁc tree and the leaf index within that tree. This allows for a somewhat
higher hypertree, as one can use a 64-bit integer for tree_idx to support parameters that
conform to h < 64 + h/d. This matches the parameters in this speciﬁcation If other parameter
sets are used that allow greater h, the data type of tree_idx MUST be adapted accordingly.
Algorithm ht_sign uses xmss_pkFromSig to compute the root node of an XMSS instance
after that instance was used for signing. An alternative is to use xmss_PKgen. However,
xmss_PKgen rebuilds the whole tree while xmss_pkFromSig only does one call to wots_pkFromSig
and (h(cid:48) − 1) calls to H. The algorithm ht_sign as described below is just one way to generate
a HT signature. Other methods MAY be used as long as they generate the same output.

# Input: Message M, private seed SK.seed, public seed PK.seed, tree index

idx_tree, leaf index idx_leaf

# Output: HT signature SIG_HT

ht_sign(M, SK.seed, PK.seed, idx_tree, idx_leaf) {

// init
ADRS = toByte(0, 32);

// sign
ADRS.setLayerAddress(0);
ADRS.setTreeAddress(idx_tree);
SIG_tmp = xmss_sign(M, SK.seed, idx_leaf, PK.seed, ADRS);

24

SIG_HT = SIG_HT || SIG_tmp;
root = xmss_pkFromSig(idx_leaf, SIG_tmp, M, PK.seed, ADRS);
for ( j = 1; j < d; j++ ) {

idx_leaf = (h / d) least significant bits of idx_tree;
idx_tree = (h - (j + 1) * (h / d)) most significant bits of idx_tree;
ADRS.setLayerAddress(j);
ADRS.setTreeAddress(idx_tree);
SIG_tmp = xmss_sign(root, SK.seed, idx_leaf, PK.seed, ADRS);
SIG_HT = SIG_HT || SIG_tmp;
if ( j < d - 1 ) {

root = xmss_pkFromSig(idx_leaf, SIG_tmp, root, PK.seed, ADRS);

}

}
return SIG_HT;

}

Algorithm 12: ht_sign – Generating an HT signature

4.2.5. HT Signature Veriﬁcation (Function ht_verify)

HT signature veriﬁcation (Algorithm 13) can be summarized as d calls to xmss_pkFromSig and
one comparison with a given value. HT signature veriﬁcation takes a message M , a signature
SIGHT, a public seed PK.seed, an index idx (split into a tree index and a leaf index, as
above), and a HT public key PKHT.

# Input: Message M, signature SIG_HT, public seed PK.seed, tree index idx_tree,

leaf index idx_leaf, HT public key PK_HT.

# Output: Boolean

ht_verify(M, SIG_HT, PK.seed, idx_tree, idx_leaf, PK_HT){

// init
ADRS = toByte(0, 32);

// verify
SIG_tmp = SIG_HT.getXMSSSignature(0);
ADRS.setLayerAddress(0);
ADRS.setTreeAddress(idx_tree);
node = xmss_pkFromSig(idx_leaf, SIG_tmp, M, PK.seed, ADRS);
for ( j = 1; j < d; j++ ) {

idx_leaf = (h / d) least significant bits of idx_tree;
idx_tree = (h - (j + 1) * h / d) most significant bits of idx_tree;
SIG_tmp = SIG_HT.getXMSSSignature(j);
ADRS.setLayerAddress(j);
ADRS.setTreeAddress(idx_tree);
node = xmss_pkFromSig(idx_leaf, SIG_tmp, node, PK.seed, ADRS);

}
if ( node == PK_HT ) {

return true;

} else {

return false;

}

}

Algorithm 13: ht_verify – Verifying a HT signature SIGHT on a message M using a HT

public key PKHT

25

5. FORS: Forest Of Random Subsets

The SPHINCS+ hypertree HT is not used to sign the actual messages but the public keys of
FORS instances which in turn are used to sign message digests. FORS (pronounced [fO:rs]),
short for forest of random subsets, is a few-time signature scheme (FTS). FORS is an im-
provement of HORST [5] which in turn is a variant of HORS [21]. For security it is essential
that the input to FORS is the output of a hash function. In the following we describe FORS
as acting on bit strings.

FORS uses parameters k and t = 2a (example parameters are t = 215, k = 10). FORS
signs strings of length ka bits. Here, we deviate from deﬁning sizes in bytes as the message
length in bits might not be a multiple of eight. The private key consists of kt random n-
byte strings grouped into k sets, each containing t n-byte strings. The private key values are
pseudorandomly generated from the main private seed SK.seed in the SPHINCS+ private key.
In SPHINCS+, the FORS private key values are only temporarily generated as an intermediate
result when computing the public key or a signature.

The FORS public key is a single n-byte hash value. It is computed as the tweakable hash
of the root nodes of k binary hash trees. Each of these binary hash trees has height a and is
used to authenticate the t private key values of one of the k sets. Accordingly, the leaves of a
tree are the (tweakable) hashes of the values in its private key set.

A signature on a string M consists of k private key values – one per set of private key
elements – and the associated authentication paths. To compute the signature, md is split
into k a-bit strings. As md is a sequence of bytes, we ﬁrst convert to a bit-string by enumerating
the bytes in md, internally enumerating the bits within a byte from least to most signiﬁcant.
Next, each of these bit strings is interpreted as an integer between 0 and t − 1. Each of these
integers is used to select one private key value from a set. I.e., if the ﬁrst integer is i, the
ith private key element of the ﬁrst set gets selected and so on. The signature consists of the
selected private key elements and the associated authentication paths.

SPHINCS+ uses implicit veriﬁcation for FORS, only using a method to compute a candidate
public key from a signature. This is done by computing root nodes of the k trees using the
indices computed from the input string as well as the private key values and authentication
paths form the signature. The tweakable hash of these roots is then returned as candidate
public key.

We now describe the parameters and algorithms for FORS.

5.1. FORS Parameters

FORS uses the parameters n, k, and t; they all take positive integer values. These parameters
are summarized as follows:

• n: the security parameter; it is the length of a private key, public key, or signature

element in bytes.

• k: the number of private key sets, trees and indices computed from the input string.

• t: the number of elements per private key set, number of leaves per hash tree and upper
bound on the index values. The parameter t MUST be a power of 2. If t = 2a, then the
trees have height a and the input string is split into bit strings of length a.

Inputs to FORS are bit strings of length k log t.

26

Figure 12: FORS trees and PK

5.2. FORS Private Key (Function fors_SKgen)

In the context of SPHINCS+, a FORS private key is the single private seed SK.seed contained
in the SPHINCS+ private key. It is used to generate the kt n-byte private key values using
PRF with a FORS key generation address. While these values are logically grouped into
a two-dimensional array, for implementations it makes sense to assume they are in a one-
dimensional array of length kt. The jth element of the ith set is then stored at sk[it + j].
To generate one of these elements, a FORS key generation address skADRS is used, that
encodes the position of the FORS key pair within SPHINCS+ and has tree height set to 0 and
leaf index set to it + j:

#Input: secret seed SK.seed, address ADRS, secret key index idx = it+j
#Output: FORS private key sk

fors_SKgen(SK.seed, ADRS, idx) {

skADRS = ADRS; // copy address to create key generation address
skADRS.setType(FORS_PRF);
skADRS.setKeyPairAddress(ADRS.getKeyPairAddress());

skADRS.setTreeHeight(0);
skADRS.setTreeIndex(idx);
sk = PRF(SK.seed, skADRS);

return sk;

}

Algorithm 14: fors_SKgen – Computing a FORS private key value.

5.3. FORS TreeHash (Function fors_treehash)

Before coming to the FORS public key, we have to discuss computation of the trees. For the
computation of the n-byte nodes in the FORS hash trees, the subroutine fors_treehash is
used. It is essentially the same algorithm as treehash (Algorithm 7) in Section 4.1. The two
diﬀerences are how the leaf nodes are computed and how addresses are handled. However,

27

...............as the addresses are similar, an implementation can implement both algorithms in the same
routine easily.

Algorithm fors_treehash accepts a secret seed SK.seed, a public seed PK.seed, an un-
signed integer s (the start index), an unsigned integer z (the target node height), and an
address ADRS that encodes the address of the FORS key pair. As for treehash, the
fors_treehash algorithm returns the root node of a tree of height z with the leftmost leaf
being the hash of the private key element at index s. Here, s is ranging over the whole kt
private key elements. It is REQUIRED that s % 2z = 0, i.e. that the leaf at index s is a
leftmost leaf of a sub-tree of height z. Otherwise the algorithm fails as it would compute
non-existent nodes.

# Input: Secret seed SK.seed, start index s, target node height z, public seed

PK.seed, address ADRS

# Output: n-byte root node - top node on Stack

fors_treehash(SK.seed, s, z, PK.seed, ADRS) {
if( s % (1 << z) != 0 ) return -1;
for ( i = 0; i < 2^z; i++ ) {

sk = fors_SKgen(SK.seed, ADRS, s+i)
node = F(PK.seed, ADRS, sk);
ADRS.setTreeHeight(1);
ADRS.setTreeIndex(s + i);
while ( Top node on Stack has same height as node ) {

ADRS.setTreeIndex((ADRS.getTreeIndex() - 1) / 2);
node = H(PK.seed, ADRS, (Stack.pop() || node));
ADRS.setTreeHeight(ADRS.getTreeHeight() + 1);

}
Stack.push(node);

}
return Stack.pop();

}

Algorithm 15: The fors_treehash algorithm.

5.4. FORS Public Key (Function fors_PKgen)

In the context of SPHINCS+, the FORS public key is never generated alone.
It is only
generated together with a signature. We include fors_PKgen for completeness, a better un-
derstanding, and testing. Algorithm fors_PKgen takes a private seed SK.seed, a public seed
PK.seed, and a FORS address ADRS. The latter encodes the position of the FORS instance
within SPHINCS+. It outputs a FORS public key.

# Input: Secret seed SK.seed, public seed PK.seed, address ADRS
# Output: FORS public key PK

fors_PKgen(SK.seed, PK.seed, ADRS) {

forspkADRS = ADRS; // copy address to create FTS public key address

for(i = 0; i < k; i++){

root[i] = fors_treehash(SK.seed, i*t, a, PK.seed, ADRS);

}
forspkADRS.setType(FORS_ROOTS);
forspkADRS.setKeyPairAddress(ADRS.getKeyPairAddress());

28

pk = T_k(PK.seed, forspkADRS, root);
return pk;

}

Algorithm 16: fors_PKgen – Generate a FORS public key.

5.5. FORS Signature Generation (Function fors_sign)

A FORS signature is a length k(log t + 1) array of n-byte strings. It contains k private key
values, n-bytes each, and their associated authentication paths, log t n-byte values each.

The algorithm fors_sign takes a (k log t)-bit string M , a private seed SK.seed, a public
seed PK.seed, and an address ADRS. The latter encodes the position of the FORS instance
within SPHINCS+. It outputs a FORS signature SIGFORS.

#Input: Bit string M, secret seed SK.seed, address ADRS, public seed PK.seed
#Output: FORS signature SIG_FORS

fors_sign(M, SK.seed, PK.seed, ADRS) {

// compute signature elements
for(i = 0; i < k; i++){

// get next index
unsigned int idx = bits i*log(t) to (i+1)*log(t) - 1 of M;

// pick private key element
SIG_FORS = SIG_FORS || fors_SKgen(SK.seed, ADRS, i*t + idx) ;

// compute auth path
for ( j = 0; j < a; j++ ) {

s = floor(idx / (2^j)) XOR 1;
AUTH[j] = fors_treehash(SK.seed, i * t + s * 2^j, j, PK.seed, ADRS);

}
SIG_FORS = SIG_FORS || AUTH;

}
return SIG_FORS;

}

Algorithm 17: fors_sign – Generating a FORS signature on string M .

The data format for a signature is given in Figure 13.

5.6. FORS Compute Public Key from Signature (Function fors_pkFromSig)

SPHINCS+ makes use of implicit signature veriﬁcation of FORS signatures. A FORS sig-
nature is used to compute a candidate FORS public key. This public key is used in further
computations (message for the signature of the XMSS tree above) and implicitly veriﬁed by
the outcome of that computation. Hence, this speciﬁcation does not contain a fors_verify
method but the method fors_pkFromSig.

The method fors_pkFromSig takes a k log t-bit string M , a FORS signature SIGFORS, a
public seed PK.seed, and an address ADRS. The latter encodes the position of the FORS
instance within the virtual structure of the SPHINCS+ key pair. First, the roots of the
k binary hash trees are computed using fors_treehash. Afterwards the roots are hashed
using the tweakable hash function Tk. The algorithm fors_pkFromSig is given as Algo-
rithm 18. The method fors_pkFromSig makes use of functions SIGFORS.getSK(i) and

29

Private key value (tree 0) (n bytes)

AUTH (tree 0) (log t · n bytes)

...

Private key value (tree k − 1) (n bytes)

AUTH (tree k − 1) (log t · n bytes)

Figure 13: FORS signature

SIGFORS.getAUTH(i). The former returns the ith secret key element stored in the signature,
the latter returns the ith authentication path stored in the signature.

# Input: FORS signature SIG_FORS, (k lg t)-bit string M, public seed PK.seed,

address ADRS

# Output: FORS public key

fors_pkFromSig(SIG_FORS, M, PK.seed, ADRS){

// compute roots
for(i = 0; i < k; i++){

// get next index
unsigned int idx = bits i*log(t) to (i+1)*log(t) - 1 of M;

// compute leaf
sk = SIG_FORS.getSK(i);
ADRS.setTreeHeight(0);
ADRS.setTreeIndex(i*t + idx);
node[0] = F(PK.seed, ADRS, sk);

// compute root from leaf and AUTH
auth = SIG_FORS.getAUTH(i);
ADRS.setTreeIndex(i*t + idx);
for ( j = 0; j < a; j++ ) {
ADRS.setTreeHeight(j+1);
if ( (floor(idx / (2^j)) % 2) == 0 ) {

ADRS.setTreeIndex(ADRS.getTreeIndex() / 2);
node[1] = H(PK.seed, ADRS, (node[0] || auth[j]));

} else {

ADRS.setTreeIndex((ADRS.getTreeIndex() - 1) / 2);
node[1] = H(PK.seed, ADRS, (auth[j] || node[0]));

}
node[0] = node[1];

}
root[i] = node[0];

}

forspkADRS = ADRS; // copy address to create FTS public key address

30

forspkADRS.setType(FORS_ROOTS);
forspkADRS.setKeyPairAddress(ADRS.getKeyPairAddress());
pk = T_k(PK.seed, forspkADRS, root);
return pk;

}
Algorithm 18: fors_pkFromSig – Compute a FORS public key from a FORS signature.

6. SPHINCS+

We now have all ingredients to describe our main construction SPHINCS+. Essentially,
SPHINCS+ is an orchestration of the methods and schemes described before. It only adds
randomized message compression and veriﬁable index generation.

6.1. SPHINCS+ Parameters

SPHINCS+ has the following parameters:

n : the security parameter in bytes.

w : the Winternitz parameter as deﬁned in Section 3.1.

h : the height of the hypertree as deﬁned in Section 4.2.1.

d : the number of layers in the hypertree as deﬁned in Section 4.2.1.

k : the number of trees in FORS as deﬁned in Section 5.1.

t : the number of leaves of a FORS tree as deﬁned in Section 5.1.

All the restrictions stated in the previous sections apply. Recall that we use a = log t.

Moreover, from these values the values m and len are computed as

• m: the message digest length in bytes. It is computed as

m = (cid:98)(k log t + 7)/8(cid:99) + (cid:98)(h − h/d + 7)/8(cid:99) + (cid:98)(h/d + 7)/8(cid:99).

While only h+k log t bits would be needed, using the longer m as deﬁned above simpliﬁes
implementations signiﬁcantly.

• len: the number of n-byte string elements in a WOTS+ private key, public key, and

signature. It is computed as len = len1 + len2, with

len1 =

(cid:24) 8n

log(w)

(cid:25)

, len2 =

(cid:22) log (len1(w − 1))
log(w)

(cid:23)

+ 1

In the following, we assume that all algorithms have access to these parameters.

31

6.2. SPHINCS+ Key Generation (Function spx_keygen)
The SPHINCS+ private key contains two elements. First, the n-byte secret seed SK.seed
which is used to generate all the WOTS+ and FORS private key elements. Second, an n-byte
PRF key SK.prf which is used to deterministically generate a randomization value for the
randomized message hash.

The SPHINCS+ public key also contains two elements. First, the HT public key, i.e. the
root of the tree on the top layer. Second, an n-byte public seed value PK.seed which is
sampled uniformly at random.

As spx_sign does not get the public key, but needs access to PK.seed (and possibly to
PK.root for fault attack mitigation), the SPHINCS+ secret key contains a copy of the public
key.

The description of algorithm spx_keygen assumes the existence of a function sec_rand

which on input i returns i-bytes of cryptographically strong randomness.

# Input: (none)
# Output: SPHINCS+ key pair (SK,PK)

spx_keygen( ){

SK.seed = sec_rand(n);
SK.prf = sec_rand(n);
PK.seed = sec_rand(n);
PK.root = ht_PKgen(SK.seed, PK.seed);
return ( (SK.seed, SK.prf, PK.seed, PK.root), (PK.seed, PK.root) );

}

Algorithm 19: spx_keygen – Generate a SPHINCS+ key pair.

The format of a SPHINCS+ private and public key is given in Figure 14.

SK.seed (n bytes)

SK.prf (n bytes)

PK.seed (n bytes)

PK.seed (n bytes)

PK.root (n bytes)

PK.root (n bytes)

Figure 14: Left: SPHINCS+ secret key. Right: SPHINCS+ public key.

6.3. SPHINCS+ Signature

A SPHINCS+ signature SIGHT is a byte string of length (1+k(a+1)+h+dlen)n. It consists
of an n-byte randomization string R, a FORS signature SIGFORS consisting of k(a+1) n-byte
strings, and a HT signature SIGHT of (h + dlen)n bytes.
The data format for a signature is given in Figure 11

32

Randomness R (n bytes)

FORS signature SIGFORS (k(a + 1) · n bytes)

HT signature SIGHT ((h + dlen)n bytes)

Figure 15: SPHINCS+ signature

6.4. SPHINCS+ Signature Generation (Function spx_sign)
Generating a SPHINCS+ signature consists of four steps. First, a random value R is pseu-
dorandomly generated. Next, this is used to compute a m byte message digest which is split
into a (cid:98)(k log t + 7)/8(cid:99)-byte partial message digest tmp_md, a (cid:98)(h − h/d + 7)/8(cid:99)-byte tree
index tmp_idx_tree, and a (cid:98)(h/d + 7)/8(cid:99)-byte leaf index tmp_idx_leaf. Next, the actual
values md, idx_tree, and idx_leaf are computed by extracting the necessary number of
bits. The partial message digest md is then signed with the idx_leaf-th FORS key pair of
the idx_tree-th XMSS tree on the lowest HT layer. The public key of the FORS key pair
is then signed using HT. As described in Section 4.2.3, the index is never actually used as a
whole, but immediately split into a tree index and a leaf index, for ease of implementation.

When computing R, the PRF takes a n-byte string opt which is initialized with PK.seed
but can be overwritten with randomness if the global variable RANDOMIZE is set. This
option is given as otherwise SPHINCS+ signatures would be always deterministic. This might
be problematic in some settings. See Section 9 and Section 11 for more details.

# Input: Message M, private key SK = (SK.seed, SK.prf, PK.seed, PK.root)
# Output: SPHINCS+ signature SIG

spx_sign(M, SK){
// init
ADRS = toByte(0, 32);

// generate randomizer
opt = PK.seed;
if(RANDOMIZE){

opt = rand(n);

}
R = PRF_msg(SK.prf, opt, M);
SIG = SIG || R;

// compute message digest and index
digest = H_msg(R, PK.seed, PK.root, M);
tmp_md = first floor((ka +7)/ 8) bytes of digest;
tmp_idx_tree = next floor((h - h/d +7)/ 8) bytes of digest;
tmp_idx_leaf = next floor((h/d +7)/ 8) bytes of digest;

md = first ka bits of tmp_md;
idx_tree = first h - h/d bits of tmp_idx_tree;
idx_leaf = first h/d bits of tmp_idx_leaf;

33

// FORS sign
ADRS.setLayerAddress(0);
ADRS.setTreeAddress(idx_tree);
ADRS.setType(FORS_TREE);
ADRS.setKeyPairAddress(idx_leaf);

SIG_FORS = fors_sign(md, SK.seed, PK.seed, ADRS);
SIG = SIG || SIG_FORS;

// get FORS public key
PK_FORS = fors_pkFromSig(SIG_FORS, md, PK.seed, ADRS);

// sign FORS public key with HT
ADRS.setType(TREE);
SIG_HT = ht_sign(PK_FORS, SK.seed, PK.seed, idx_tree, idx_leaf);
SIG = SIG || SIG_HT;

return SIG;

}

Algorithm 20: spx_sign – Generating a SPHINCS+ signature

6.5. SPHINCS+ Signature Veriﬁcation (Function spx_verify)
SPHINCS+ signature veriﬁcation (Algorithm 21) can be summarized as recomputing message
digest and index, computing a candidate FORS public key, and verifying the HT signature on
that public key. Note that the HT signature veriﬁcation will fail if the FORS public key is
not matching the real one (with overwhelming probability). SPHINCS+ signature veriﬁcation
takes a message M , a signature SIG, and a SPHINCS+ public key PK.

# Input: Message M, signature SIG, public key PK
# Output: Boolean

spx_verify(M, SIG, PK){

// init
ADRS = toByte(0, 32);
R = SIG.getR();
SIG_FORS = SIG.getSIG_FORS();
SIG_HT = SIG.getSIG_HT();

// compute message digest and index
digest = H_msg(R, PK.seed, PK.root, M);
tmp_md = first floor((ka +7)/ 8) bytes of digest;
tmp_idx_tree = next floor((h - h/d +7)/ 8) bytes of digest;
tmp_idx_leaf = next floor((h/d +7)/ 8) bytes of digest;

md = first ka bits of tmp_md;
idx_tree = first h - h/d bits of tmp_idx_tree;
idx_leaf = first h/d bits of tmp_idx_leaf;

// compute FORS public key
ADRS.setLayerAddress(0);
ADRS.setTreeAddress(idx_tree);
ADRS.setType(FORS_TREE);
ADRS.setKeyPairAddress(idx_leaf);

34

PK_FORS = fors_pkFromSig(SIG_FORS, md, PK.seed, ADRS);

// verify HT signature
ADRS.setType(TREE);
return ht_verify(PK_FORS, SIG_HT, PK.seed, idx_tree, idx_leaf, PK.root);

}

Algorithm 21: spx_verify – Verify a SPHINCS+ signature SIG on a message M using a

SPHINCS+ public key PK

7. Instantiations

This section discusses instantiations for SPHINCS+. SPHINCS+ can be viewed as a signature
template. It is a way to build a signature scheme by instantiating the cryptographic function
families used. We consider diﬀerent ways to implement the cryptographic function families
as diﬀerent signature systems. Orthogonal to instantiating the cryptographic function fami-
lies are parameter sets. Parameter sets assign speciﬁc values to the SPHINCS+ parameters
described in Section 7.1 below.

In this section, we ﬁrst deﬁne the requirements on parameters and discuss existing trade-oﬀs
between security, sizes, and speed controlled by the diﬀerent parameters. Then we propose 6
diﬀerent parameter sets that match NIST security levels I, III, and V (2 parameter sets per
security level). Afterwards we propose three diﬀerent instantiations for the cryptographic func-
tion families of SPHINCS+. These instantiation are indeed three diﬀerent signature schemes.
We propose SPHINCS+-SHAKE, SPHINCS+-SHA2, and SPHINCS+-Haraka. The former
two use the cryptographic hash functions deﬁned in FIPS PUB 202, respectively FIPS PUB
180, to instantiate the cryptographic function families. The latter uses a new cryptographic
(hash) function called Haraka, proposed in [14].

7.1. SPHINCS+ Parameter Sets

SPHINCS+ is described by the following parameters already described in the previous sections.
All parameters take positive integer values.

n : the security parameter in bytes.

w : the Winternitz parameter.

h : the height of the hypertree.

d : the number of layers in the hypertree.

k : the number of trees in FORS.

t : the number of leaves of a FORS tree.

Recall that we use a = log t. Moreover, from these values the values m and len are computed

as

• m: the message digest length in bytes. It is computed as m = (cid:98)(k log t + 7)/8(cid:99) + (cid:98)(h −

h/d + 7)/8(cid:99) + (cid:98)(h/d + 7)/8(cid:99).

35

• len: the number of n-byte string elements in a WOTS+ private key, public key, and

signature. It is computed as len = len1 + len2, with

len1 =

(cid:24) 8n

log(w)

(cid:25)

, len2 =

(cid:22) log (len1(w − 1))
log(w)

(cid:23)

+ 1

We now repeat the roles of, requirements on, and properties of these parameters. After-
wards, we give several formulas that show their exact inﬂuence on performance and security.
The security parameter n is also the output length of all cryptographic function families
besides Hmsg. Therefore, it largely determines which security level a parameter set reaches.
It is also the size of virtually any node within the SPHINCS+ structure and thereby also the
size of all elements in a signature, i.e., the signature size is a multiple of n.

The Winternitz parameter w determines the number and length of the hash chains per
WOTS+ instance. A greater value for w linearly increases the length of the hash chains
but logarithmically reduces their number. The number of hash chains exactly corresponds
to the number of n-byte values in a WOTS+ signature. Thereby it largely inﬂuences the
size of a SPHINCS+ signature. The product of the number and the length of hash chains
directly correlates with signing speed as essentially all time in HT signature generation is
spent computing WOTS+ public keys. Therefore, greater w means shorter signatures but
slower signing. However, note the exponential gap. The bigger w gets, the more expensive
is the signature size reduction. The Winternitz parameter does not inﬂuence SPHINCS+
security.

The height of the hypertree h determines the number of FORS instances. Hence, it de-
termines the probability that a FORS key pair is used several times, given the number of
signatures made with a SPHINCS+ key pair. Hence, the height has a direct impact on secu-
rity: A taller hypertree gives more security. On the other hand, a taller tree leads to larger
signatures.

The number of layers d is a pure performance trade-oﬀ parameter and does not inﬂuence
security. It determines the number of layers of XMSS trees in the hypertree. Hence, d must
divide h without remainder. The parameter d thereby deﬁnes the height of the XMSS trees
used. The greater d, the smaller the subtrees, the faster signing. However, d also controls the
number of layers and thereby the number of WOTS+ signatures within a HT and thereby a
SPHINCS+ signature.

The parameters k and t determine the performance and security of FORS. The number of
leaves of a tree in FORS t must be a power of two while k can be chosen freely. A smaller t
generally leads to smaller and faster signatures. However, for a given security level a smaller
t requires a greater k which increases signature size and slows down signing. Hence, it is
important to balance these two parameters. This is best done using the formulas below.

The message digest length m is the output length of Hmsg in bytes. It is (cid:98)(k log t + 7)/8(cid:99) +

(cid:98)(h − h/d + 7)/8(cid:99) + (cid:98)(h/d + 7)/8(cid:99) bytes.

The number len of chains in a WOTS+ key pair determines the WOTS+ signature size.

7.1.1. Inﬂuence of Parameters on Security and Performance

In the following we provide formulas to compute speed, size and security for a given SPHINCS+
parameter set. This supports parameter selection. We also provide a SAGE script in Ap-
pendix A.

36

Table 1: Overview of the number of function calls we require for each operation. We omit the
single calls to Hmsg, PRFmsg, and Tk for signing and single calls to Hmsg and Tk
for veriﬁcation as they are negligible when estimating speed.

F

H

PRF

Key Generation
Signing
Veriﬁcation

2h/dwlen
kt + d(2h/d)wlen
k + dwlen

2h/d − 1
k(t − 1) + d(2h/d − 1)
k log t + h

2h/dlen
kt + d(2h/d)len
–

Tlen
2h/d
d2h/d
d

Table 2: Key and signature sizes

SK PK

Sig

Size

4n

2n

(h + k(log t + 1) + d · len + 1)n

Key Generation. Generating the SPHINCS+ private key and PK.seed requires three calls
to a secure random number generator. Next we have to generate the top tree. For the leaves
we need to do 2h/d WOTS+ key generations (len calls to PRF for generating the sk and
wlen calls to F for the pk) and we have to compress the WOTS+ public key (one call to Tlen).
Computing the root of the top tree requires (2h/d − 1) calls to H.

Signing. For randomization and message compression we need one call to PRFmsg, and one
to Hmsg. The FORS signature requires kt calls to PRF and F. Further, we have to compute
the root of k binary trees of height log t which adds k(t − 1) calls to H. Finally, we need one
call to Tk. Next, we compute one HT signature which consists of d trees similar to the key
generation. Hence, we have to do d(2h/d) times len calls to PRF and wlen calls to F as well
as d(2h/d) calls to Tlen. For computing the root of each tree we get additionally d(2h/d − 1)
calls to H.

Veriﬁcation. First we need to compute the message hash using Hmsg. We need to do one
FORS veriﬁcation which requires k calls to F (to compute the leaf nodes from the signature
elements), k log t calls to H (to compute the root nodes using the leaf nodes and the authen-
tication paths), and one call to Tk for hashing the roots. Next, we have to verify d XMSS
signatures which takes < wlen calls to F and one call to Tlen each for WOTS+ signature
veriﬁcation1. It also needs dh/d calls to H for the d root computations.

The size of the SPHINCS+ private and public keys along with the signature can be deduced
from Section 6 and is shown in Table 2.

The classical security level, or bit security of SPHINCS+ against generic attacks can be

1It should be noted that the wlen bound for calls to F is a worst-case bound. This is a bound on the
cost for WOTS signature veriﬁcation. Given that the messages are hash values which can assumed to be
close to uniformly distributed, this value will be closer to the average-case bound (w/2) · len in actual
measurements.

37

computed as

b = − log

(cid:32)

1
28n +

(cid:18)

(cid:88)

(cid:18)

1 −

1 −

γ

(cid:19)γ(cid:19)k (cid:18)q
γ

1
t

(cid:19) (cid:18)

1 −

1
2h

(cid:19)q−γ 1
2hγ

(cid:33)

.

The quantum security level, or bit security of SPHINCS+ against generic attacks can be

computed as

b = −

(cid:32)

1
2

log

1
28n +

(cid:18)

(cid:88)

(cid:18)

1 −

1 −

γ

(cid:19)γ(cid:19)k (cid:18)q
γ

1
t

(cid:19) (cid:18)

1 −

1
2h

(cid:19)q−γ 1
2hγ

(cid:33)

.

Here, we are neglecting the small constant factors inside the logarithm. For details see Sec-
tion 9.

7.1.2. Proposed Parameter Sets and Security Levels

As explained in the previous subsection, even for a ﬁxed security level the design of SPHINCS+
In Table 3 we list 6
supports many diﬀerent tradeoﬀs between signature size and speed.
parameter sets that—together with the cycle counts given in Table 4— illustrates how these
tradeoﬀs can be used to obtain concrete parameter sets optimizing for signature size and
concrete parameter sets optimizing for speed. Speciﬁcally, we propose parameter sets achieving
security levels 1, 3, and 5; for each of these security levels propose one size-optimized (ending
on ‘s’ for “small”) and one speed-optimized (ending on ‘f’ for “fast”) parameter set. The
parameter sets were obtained with the help of a Sage script that we list in Appendix A. In
the ﬁrst line of that script, set the “target bit security” to a desired value (in our case, close
to 128 for security level 1, close to 192 for security level 3, and close to 256 for security level
5). The output of the script will be a long list of possible parameters achieving this security
level together with the signature size and an estimate of the performance, using the formulas
from Section 7.1.1 above.

Note that we did not obtain our proposed parameter sets simply by searching this output
for the smallest or the fastest option. The reason is that, for example, optimizing for size
without caring about speed at all results in signatures of a size of ≈ 15 KB for a bit security
of 256, but computing one signature takes more than 20 minutes on our benchmark platform.
Such a tradeoﬀ might be interesting for very few select applications, but we cannot think of
many applications that would accept such a large time for signing.
Instead, the proposed
parameter sets are what we consider “non-extreme”; i.e., with a signing time of at most a few
seconds in our non-optimized implementation.

The choice of these parameters is orthogonal to the choice of hash function. In Section 7.2
we describe three diﬀerent instantiations of the underlying hash function, each with a simple
and a robust variant. Together with the six parameter sets listed in Table 3 we obtain 36
diﬀerent instantiations of SPHINCS+.

7.2. Instantiations of Hash Functions

In this section we deﬁne diﬀerent signature schemes, which are obtained by instantiating
the cryptographic function families of SPHINCS+ with SHA2, SHAKE, and Haraka. To
instantiate the tweakable hash functions, we present two diﬀerent constructions. Leading
to a total of six instantiations. For the ‘robust’ instances, we ﬁrst generate pseudorandom

38

Table 3: Example parameter sets for SPHINCS+ targeting diﬀerent security levels and dif-
ferent tradeoﬀs between size and speed. Note that these parameter sets have been
update for round 3. The column labeled “bitsec” gives the bit security computed as
described in Section 9; the column labeled “sec level” gives the security level according
to the levels speciﬁed in Section 4.A.5 of the Call for Proposals. As explained later,
for Haraka the security level is limited to 2: i.e., it is 1 for n = 16, and 2 for
n = 24 or n = 32.

SPHINCS+-128s
SPHINCS+-128f
SPHINCS+-192s
SPHINCS+-192f
SPHINCS+-256s
SPHINCS+-256f

n

16
16
24
24
32
32

h

63
66
63
66
64
68

d

7
22
7
22
8
17

log(t)

12
6
14
8
14
9

k w bitsec
133
14
128
33
193
17
194
33
255
22
255
35

16
16
16
16
16
16

sec level

sig bytes

1
1
3
3
5
5

7 856
17 088
16 224
35 664
29 792
49 856

bitmasks which are then XORed with the input message. The masked messages are denoted
as M ⊕. For the ‘simple’ instances, we take an approach inspired by the LMS proposal for
stateful hash-based signatures [16], and omit the bitmasks. We make this diﬀerence explicit
in the instances deﬁned below. The ’simple’ instances are faster as they omit the calls to the
underlying hash function to generate bitmasks. When combined with compressed addresses in
the SHA2 case this can lead to an estimated reduction of the number of compression function
calls by a factor of almost 4. In return, this comes at the cost of a security argument that
entirely relies on the random oracle model.

Recall that n and m are the security parameter and the message digest length, in bytes.

7.2.1. SPHINCS+-SHAKE

For SPHINCS+-SHAKE we deﬁne

Hmsg(R, PK.seed, PK.root, M ) = SHAKE256(R||PK.seed||PK.root||M, 8m),
PRF(PK.seed, SK.seed, ADRS) = SHAKE256(PK.seed||ADRS||SK.seed, 8n),

(1)

PRFmsg(SK.prf, OptRand, M ) = SHAKE256(SK.prf||OptRand||M, 8n).

For the robust variant, we further deﬁne the tweakable hash functions as

F(PK.seed, ADRS, M1) = SHAKE256(PK.seed||ADRS||M ⊕
H(PK.seed, ADRS, M1||M2) = SHAKE256(PK.seed||ADRS||M ⊕

1 , 8n),
1 ||M ⊕
T(cid:96)(PK.seed, ADRS, M ) = SHAKE256(PK.seed||ADRS||M ⊕, 8n),

2 , 8n),

For the simple variant, we instead deﬁne the tweakable hash functions as

F(PK.seed, ADRS, M1) = SHAKE256(PK.seed||ADRS||M1, 8n),
H(PK.seed, ADRS, M1||M2) = SHAKE256(PK.seed||ADRS||M1||M2, 8n),
T(cid:96)(PK.seed, ADRS, M ) = SHAKE256(PK.seed||ADRS||M, 8n),

39

(2)

(3)

Generating the Masks. SHAKE can be used as an XOF which allows us to generate the
bitmasks for arbitrary length messages directly. For a message M with l bits we compute

M ⊕ = M ⊕ SHAKE256(PK.seed||ADRS, l).

7.2.2. SPHINCS+-SHA2

In a similar way we deﬁne the functions for SPHINCS+-SHA2.
In some places we use
SHA2-256 for n = 16 and SHA2-512 for n = 24 and n = 32. For this we use the short-
hand SHA-X.

Hmsg(R, PK.seed, PK.root, M )

= MGF1-SHA-X(R||PK.seed||SHA-X(R||PK.seed||PK.root||M ), m),

PRF(PK.seed, SK.seed, ADRS) = SHA2-256(BlockPad(PK.seed)||ADRSc||SK.seed),

PRFmsg(SK.prf, OptRand, M ) = HMAC-SHA-X(SK.prf, OptRand||M ),

(4)
For n = 32, we only take the ﬁrst 32 bytes of output of PRF and discard the rest. For the

robust variant, we further deﬁne the tweakable hash functions as

F(PK.seed, ADRS, M1) = SHA2-256(BlockPad(PK.seed)||ADRSc||M ⊕
H(PK.seed, ADRS, M1||M2) = SHA-X(BlockPad(PK.seed)||ADRSc||(M1||M2)⊕),
T(cid:96)(PK.seed, ADRS, M ) = SHA-X(BlockPad(PK.seed)||ADRSc||M ⊕),

1 ),

For the simple variant, we instead deﬁne the tweakable hash functions as

F(PK.seed, ADRS, M1) = SHA2-256(BlockPad(PK.seed)||ADRSc||M1),

H(PK.seed, ADRS, M1||M2) = SHA-X(BlockPad(PK.seed)||ADRSc||M1||M2),
T(cid:96)(PK.seed, ADRS, M ) = SHA-X(BlockPad(PK.seed)||ADRSc||M ),

(5)

(6)

Here, we use MGF1 as deﬁned in RFC 2437 and HMAC as deﬁned in FIPS-198-1. Note

that MGF1 takes as the last input the output length in bytes.

Generating the Masks. SHA2 can be turned into a XOF using MGF1 which allows us to
generate the bitmasks for arbitrary length messages directly. The function MGF1 is used with
depends on the function in which the result is used. For F we use

M ⊕ = M ⊕ MGF1-SHA2-256(PK.seed||ADRSc, n).

For H and T, when called with a message M with l bytes we compute

M ⊕ = M ⊕ MGF1-SHA-X(PK.seed||ADRSc, l).

Padding PK.seed. Each of the instances of the tweakable hash function take PK.seed as
its ﬁrst input, which is constant for a given key pair – and, thus, across a single signature.
This leads to a lot of redundant computation. To remedy this, we pad PK.seed to the length
of a full 64-/128-byte SHA2 input block using

BlockPad(PK.seed) = PK.seed||toByte(0, bl − n).

where bl = 64 for SHA2-256 and bl = 128 for SHA2-512. Because of the Merkle-Damgård
construction that underlies SHA2, this allows for reuse of the intermediate SHA2 state after
the initial call to the compression function which improves performance.

40

Compressing ADRS. To ensure that we require the minimal number of calls to the SHA2
compression function, we use a compressed ADRS for each of these instances. Where possible,
this allows for the SHA2 padding to ﬁt within the last input block. Rather than storing the
layer address and type ﬁeld in a full 4-byte word each, we only include the least-signiﬁcant byte
of each. Similarly, we only include the least-signiﬁcant 8 bytes of the 12-byte tree address. This
reduces the address from 32 to 22 bytes. We denote such compressed addresses as ADRSc.

Shorter Outputs.
and PRFmsg we take the ﬁrst n bytes of the output and discard the remaining.

If a parameter set requires an output length n < 32-bytes for F, H, PRF,

7.2.3. SPHINCS+-Haraka

Our third instantiation is based on the Haraka short-input hash function. Haraka is not
a NIST-approved hash function, and since it is new it needs further analysis. We specify
SPHINCS+-Haraka as third signature scheme to demonstrate the possible speed-up by using
a dedicated short-input hash function.

As the Haraka family only supports input sizes of 256 and 512 bits we extend it with a
sponge-based construction based on the 512-bit permutation π. The sponge has a rate of 256-
bit respectively a capacity of 256-bit and the number of rounds used in π is 5. The padding
scheme is the same as deﬁned in FIPS PUB 202 for SHAKE.

We denote this sponge as HarakaS(M, d), where M is the padded message and d is the
length of the message digest in bits. A 256-bit message block Mi is absorbed into the state S
by

Absorb(M, S) : S = π(S ⊕ (M ||toByte(0, 32))).

The d-bit hash output h is computed by squeezing blocks of r bits

Squeeze(S) : h = h||Trunc256(S)
S = π(S).

(7)

(8)

For a more eﬃcient construction we generate the round constants of Haraka using PK.seed.2
As PK.seed is the same for all hash function calls for a given key pair we expand PK.seed
using HarakaS and use the result for the round constants in all instantiations of Haraka used
in SPHINCS+. In total there are 40 128-bit round constants deﬁned by

RC0, . . . , RC39 = HarakaS(PK.seed, 5120).

(9)

This only has to be done once for each key pair for all subsequent calls to Haraka hence
the costs for this are amortized. We denote Haraka with the round constants derived from
PK.seed as HarakaPK.seed. We can now deﬁne all functions we need for SPHINCS+-Haraka
as

Hmsg(R, PK.seed, PK.root, M ) = HarakaSPK.seed(R||PK.root||M, 8m),
PRF(PK.seed, SK.seed, ADRS) = Haraka512PK.seed(ADRS||SK.seed),

(10)

PRFmsg(SK.prf, OptRand, M ) = HarakaSPK.seed(SK.prf||OptRand||M, 8n).

2This is similar to the ideas used for the MDx-MAC construction [20].

41

For the robust variant, we further deﬁne the tweakable hash functions as

H(PK.seed, ADRS, M1||M2) = HarakaSPK.seed(ADRS||M ⊕

F(PK.seed, ADRS, M1) = Haraka512PK.seed(ADRS||M ⊕

1 ),
1 ||M ⊕
T(cid:96)(PK.seed, ADRS, M ) = HarakaSPK.seed(ADRS||M ⊕, 8n),

2 , 8n),

For the simple variant, we instead deﬁne the tweakable hash functions as

F(PK.seed, ADRS, M1) = Haraka512PK.seed(ADRS||M1),
H(PK.seed, ADRS, M1||M2) = HarakaSPK.seed(ADRS||M1||M2, 8n),
T(cid:96)(PK.seed, ADRS, M ) = HarakaSPK.seed(ADRS||M, 8n),

(11)

(12)

For F we pad M1 and M ⊕
1

with zero if n < 32. Note that H and Hmsg will always have a

diﬀerent ADRS and we therefore do not need any further domain separation.

Generating the Masks. The mask for the message used in F is generated by computing

M ⊕

1 = M1 ⊕ Haraka256PK.seed(ADRS)

(13)

For all other purposes the masks are generated using HarakaS. For a message M with l

bytes we compute

M ⊕ = M ⊕ HarakaSPK.seed(ADRS, l).

Shorter Outputs.
we take the ﬁrst n bytes of the output and discard the remaining.

If a parameter set requires an output length n < 32-bytes for F and PRF,

Security Restrictions. Note that our instantiation using Haraka employs the sponge con-
struction with a capacity of 256-bits. Hence, in contrast to SPHINCS+-SHA2 and SPHINCS+-
SHAKE, SPHINCS+-Haraka reaches security level 2 for 32- and 24-byte outputs and security
level 1 for 16-byte outputs.

8. Design rationale

The design rationale behind SPHINCS+ is to follow the original SPHINCS construction and
apply several results from more recent research. The idea behind SPHINCS was as follows.
One can build a stateless hash-based signature scheme using a massive binary certiﬁcation
tree and selecting a leaf at random for each message to be signed. The problem with this
approach is that the tree has to be extremely high, i.e., a height of about twice the security
level would be necessary. This leads to totally unpractical signature sizes. Using a hypertree
instead of a binary certiﬁcation tree allows to trade speed for signature size. However, this is
still not suﬃcient to get practical sizes and speed.

The main new idea in SPHINCS was to not use the leaves directly to sign messages but
to use the leaves to certify FTS key pairs. This allowed to massively reduce the total tree
height (by a factor about 4). This is due to the fact that the security of an FTS instance
degrades with every signature a key pair is used for. Hence, the height of the tree does not
have to be such that collisions do only occur with negligible probability anymore. Instead, it
has to be ensured that the product of the probability of a γ-times collision on a leaf and the

42

forging probability of an adversary after seeing γ FTS signatures (with the same key pair) is
negligible.

From this, it is mainly a question of balancing parameters to ﬁnd a practical scheme. For

the full original reasoning see [5].

In the following we give a more detailed reasoning regarding the changes made to SPHINCS
in SPHINCS+, and changes that were discussed by the SPHINCS+ team but got discarded.

8.1. Changes Made

We changed several details of SPHINCS leading to SPHINCS+. The reasoning behind those
changes is discussed in the following.

8.1.1. Multi-Target Attack Protection

SPHINCS was designed to be collision-resilient i.e., to not be vulnerable to collision attacks
against the used hash function. This had two reasons. First, it allowed to choose a smaller
output length at the same security level which led to smaller signatures. Second, collision
resistance is a far stronger assumption than the used (second-)preimage resistance and pseu-
dorandomness assumptions.

However, the use of (second-)preimage resistance introduced a new issue as pointed out
in [11]: Multi-target attacks. Preimage resistance properties are targeted properties. An
adversary is asked to invert the function on a given target value, or to ﬁnd a second-preimage
for a given target value. If it suﬃces to break the given property for one out of many targets,
the adversarial eﬀort is reduced by a factor of the number of targets. To prevent this in our
we apply the mitigation techniques from [11] using keyed hash functions. Each hash function
call is keyed with a diﬀerent key and applies diﬀerent bitmasks. Keys are derived from, and
bitmasks are pseudorandomly generated from a public seed and an address specifying the
context of the call. For this we introduce the notion of tweakable hash functions which take
in addition to the input value a public seed and an address.

This pseudorandom generation of bitmasks comes at the cost of introducing a random
oracle assumption for the PRF used to generate the bitmasks. However, this only applies to
I.e., if all bitmasks would be stored in the
the pseudorandom generation of the bitmasks.
public key, the scheme would have a standard model security proof (even if these bitmasks
where generated using exactly the same way but without giving away the seed). Hence, the
security reduction in [11] is in the quantum-accessible random oracle model.

One diﬀerence to [11] is that in all instantiations of SPHINCS+, keys are not pseudoran-
domly generated. Instead, the concatenation of public seed and address is used to practically
key the functions. Given how the tweakable hash functions are instantiated, this means that
we assume that there do not exist any (exponentially large) subsets of the domain on which
second-preimage ﬁnding is easy. This assumption holds for any hash function based on the
sponge or Merkle-Dåmgard construction, assuming the block or compression function behaves
like a random function.

8.1.2. Tree-less WOTS+ Public Key Compression

SPHINCS+ compresses the end nodes of the WOTS+ hash chains with a single call to a
tweakable hash function, while SPHINCS used a so called L-tree. The reason to use L-trees in
SPHINCS was that this required only two n-byte bitmasks per layer, i.e., 2(cid:100)log len(cid:101) bitmasks.

43

A single call to a tweakable hash requires len n-byte bitmasks. As the bitmasks were stored
in the public key, this meant smaller public keys. Now, that bitmasks are pseudorandomly
generated anyway and hence are not stored in the public key anymore, this argument does
not apply. On the opposite, tree based compression is slower than using a single call to a
tweakable hash with longer input.

8.1.3. FORS

FORS was used to replace HORST. HORST, as its predecessor HORS, had the problem
that weak messages existed as recently independently pointed out in [1]. More speciﬁcally,
in HORST the message is also split into k indexes as for FORS. However, these indexes all
selected values from the same single set of secret key values. Hence, if the same index appeared
multiple times in a signature, still only a single secret value would be required. In extreme
cases this means that for the signature of a message only a single secret value has to be know.
FORS prevents this using separate secret value sets per index obtained from the message.
Even if a message maps k-times to the same index, the signature now contains k diﬀerent
secret values.

For the same parameters k and t this would mean an increase in signature size and worse
speed as now k trees of height log t have to be computed instead of one and for each signature
value an authentication path of length (log t) − 1 is needed. However, due to the strengthened
security, we can choose diﬀerent values for k and t. This in the end leads to smaller signatures
than for HORST.

We also considered a method similar to Octopus [2]. The idea is that authentication paths in
HORST largely overlap. Hence, it becomes possible to reduce the signature size removing any
redundancy in the authentication paths. This comes at the cost of a rather involved method
to collect the right nodes as well as variable size signatures. In practice this means that one
still has to prepare for the worst case. This worst case indeed still has smaller signatures than
HORST. We decided against this option as the FORS signature size matches that of Octopus’
worst case signature size. At the same time, FORS gives more ﬂexibility in the choice of k
and t, and comes with a far simpler signature and veriﬁcation method that Octopus.

8.1.4. Veriﬁable Index Selection

In SPHINCS the index of the HORST instance to be used was pseudorandomly selected. This
had the drawback that the index appeared random to a veriﬁer and it was impossible to verify
that the index was indeed generated that way. This allowed an adversary a multi-target attack
on HORST (similarly for FORS in SPHINCS+). An adversary could ﬁrst map a message to
an index set and then check if the necessary secret values were already uncovered for some
HORST key pair. Then it would just select the index of that HORST key pair as index and
succeed in forging a signature.

To prevent this attack, we decided to make index generation veriﬁable. More speciﬁcally,

we generate the index together with the message digest:

We compute message digest and index as

where PK = (PK.seed||PK.root) contains the top root node and the public seed.

(md||idx) = Hmsg(R, PK, M )

44

This way, an adversary can no longer freely choose an index. Indeed, selecting a message
immediately also ﬁxes the index. This method has another advantage in addition to avoiding
the multi-target attack against FORS/HORST. We can omit the index in the SPHINCS
signature as it would be redundant.

8.1.5. Making Deterministic Signing Optional

The pseudorandom generation of randomizer R now allows to use additional randomness. It
takes a n-byte value OptRand. Per default OptRand is set to 0 but it can be ﬁlled with random
bits e.g. taken from a TRNG. The randomizer is then computed as

R = PRF(SK.prf, OptRand, M ).

That way, deterministic signing becomes optional. Deterministic signing can be a problem
for devices which are susceptible to side-channel attacks as it allows to collect several traces
for the exactly same computation by just asking for a signature on the same message multiple
times.

We could of course also have replaced R by a truly random value on default. This would
have caused the scheme to become susceptible to bad randomness. The new method prevents
this. If OptRand is a high entropy string, R has as much entropy as that string. If OptRand is
left as zero or has only little entropy, R is just a pseudorandom value as in SPHINCS.

8.1.6. SPHINCS+-’simple’ and SPHINCS+-’robust’

The updated, Round 2 submission of SPHINCS+ introduces instantiations of the tweakable
hash functions similar to those of the LMS proposal for stateful hash-based signatures [16].
These instantiations are called ’simple’ (compared to the established instantiations which we
now call ’robust’). The ’simple’ instantiations omit the use of bitmasks, i.e., no bitmasks have
to be generated and XORed with the message input of the tweakable hash functions F, H
or T. This has the advantage of better speed since the calls to the underlying hash function
(needed in order to generate the bitmasks for each tweakable hash calculation) are saved.
However, the resulting drawback is a security argument which in its entirety only applies in
the random oracle model.

Another reason to propose these simple instantiations is the possibility to align the con-
struction with the stateful scheme [16] such that clients can easily implement the veriﬁca-
tion procedure for both with a small code-base, as for the robust instantiations and XMSS.
However, the simple instantiations of SPHINCS+ are so far not compatible with the LMS
signature scheme as described in [16]. The simple instantiations of SPHINCS+ uses PK.seed
and ADRS to distinguish hash calls. LMS uses a specially crafted security string which has
the same purpose, is similar, but diﬀers in the details.

Most of the time in SPHINCS+, XMSS, and LMS, is spent on F computations. The LMS
proposal [16] optimized the length of their security string for SHA2 to ensure that the F
computations of the OTS signatures can be done with a single compression function call.
We use a similar approach, applied to our SPHINCS+-SHA2 instantiation. For this purpose
we compress the hash addresses in case of SHA2 instantiations and pad PK.seed to ﬁt a
full compression function block (with an exception of the mask generation). As PK.seed is
constant for a key pair, this allows to precompute the internal state of SHA2 after absorbing
this block and reduce the necessary online computations to a single compression function call

45

for the SHA2-simple instances. Also for the robust instantiations this saves a factor of two in
compression function calls. For SHAKE and Haraka such an optimization is of no eﬀect as
one F computation already takes only a single call to the inner function.

8.2. Discarded Changes

In Section 8.1.3, we already explained that we discarded the use of an Octopus-like method
as we found a better alternative.

One more idea which we discarded on the way was a signature - secret key size trade-oﬀ. To
further shrink the SPHINCS+ signature size, the top z layers of the hypertree can be merged
together into a a single tree of height zh(cid:48). That way an SPHINCS+ signature includes z − 1
less WOTS+ signatures. This decreases the signature size by n · len(z − 1) bytes, but typically
comes at the cost of speed as now a tree of height zh(cid:48) has to be computed for each signature
generation. This can be prevented by storing the nodes at height ih(cid:48), where 0 < i < z, as
part of the secret key. These nodes (auxiliary data) can be used to build the authentication
paths to the root of the merged tree without actually computing the whole tree.
Indeed,
authentication path computation in this case gets faster than computing the authentication
paths for z tree layers in the original hypertree. The size of the auxiliary data is n (cid:80)z−1
i=1 2ih(cid:48).
While this already grows extremely fast, the real problem turned out to be key generation
time. As the full tree still has to be computed once during key generation, key generation
time increases. Key generation would now take 2zh(cid:48) WOTS+ key generations.

Initial experiments suggested that key generation time easily moves into the order of minutes
already for z = 2 while the beneﬁt in signature size is 1KB or 2KB for w = 256 and w = 16
respectively. In addition, this optimization signiﬁcantly complicates implementations as the
top tree has to be handled diﬀerently than the remaining trees. Hence, this idea was discarded.

9. Security Evaluation (including estimated security strength

and known attacks)

The security of SPHINCS+ is based on standard model properties of the used function families
/ tweakable hash functions. These in turn can be derived from the properties of the hash func-
tions used to instantiate those function families. For the robust instantiations, these properties
can be derived from standard model properties of the used hash function and for some the
assumption that the PRF used within the instantiations of the tweakable hash functions (to
generate the bitmasks) can be modeled as a random oracle. We want to emphasize once more
that this assumption about the random oracle is limited to the pseudorandom generation of
bitmasks. For the simple instantiations, these properties can be derived from the assumption
that the used hash function behaves like a random oracle even in the presence of quantum
adversaries which are given quantum oracle access to the function.

In [12], the security of SPHINCS+ is tightly related to

• the PRF-security of PRF and PRFmsg,

• the interleaved-target-subset-resilience (ITSR) of Hmsg,

• the single function, multi-target undetectability (SM-UD), target-collision (SM-TCR),

and decisional second-preimage resistance (SM-DSPR) of F, and

46

• the SM-TCR security of H and T(cid:96).

There were several attempts at proving tight security for SPHINCS+ which were shown to
be ﬂawed after publication. The challenging part is proving tight security for the used WOTS
scheme in a multi-instance setting. On November 2, 2021, we announced a new tight security
proof for SPHINCS+ as oﬃcial comment. This security analysis can be found in [12]. While
this new proof clearly has to be vetted, we want to highlight that at no point the actual
security of SPHINCS+ was challenged.

Disclaimer: The following two subsections present an attempt for a tight security reduction
for SPHINCS+ that turned out to be ﬂawed; we keep them here for reference. The ﬂaw is an
artifact of the attempt to prove a tight security reduction for the variant of the Winternitz
one-time signature scheme used by SPHINCS+ taken from [11]. It should be noted that the
non-tight proof for WOTS+ from [9] still applies. Also, the ﬂaw does not translate into an
attack but just demonstrates that the proof made false assumptions. Indeed, at the time of
writing we are positive that the problem can be circumvented. Hence, this does not inﬂuence
our security estimates at all. In the following we brieﬂy outline the ﬂaw and a previous issue
with the security reduction and discuss the solution.

A ﬁrst issue was ﬁxed since version 2 of this speciﬁcation and appeared in the scientiﬁc pub-
lication on SPHINCS+ [3]. That work also discusses the security of the simple instantiations,
not discussed below. The issue was as follows. The security reduction makes a statistical
assumption about the used hash function which does not hold for a random function and,
consequently, should not hold for a good cryptographic hash function. This assumption essen-
tially states that every possible input to F has at least one colliding value under F (which we
call sibling). It is trivial to construct a hash function for which it is reasonable to conjecture
this property. Just take for example SHA2-256, apply it once, truncate the result to 248 bits
and apply SHA2-256 again. However, this would have to be paid for by a factor 2 penalty in
speed.

The ﬂaw which persisted also in [3] is related to the same part of the proof. It is concerned
with arguing about the hardness of ﬁnding x given y = F(x). The above assumption was used
to argue that if y has at least two preimages x, x(cid:48), it is information theoretically hidden from
an adversary which preimage was used to compute y. This argument applies if x is chosen
uniformly at random from the whole domain, and if no side-information about x exists. It
does not necessarily apply if the input is known to be an output of another function (in our
case F). Intuitively what is required under this condition is that this side-information does
still not allow an adversary to determine which preimage was used to compute y. This can be
shown using the additional assumption that F is undetectable, as used in previous works (e.g.,
[9]). Undetectability says that an image of a function on a random input is indistinguishable
from a random element in its codomain.

Reductionist proof. In this section we give a security reduction for SPHINCS+ underpinning
the above claim. The security reduction essentially combines the original SPHINCS security
reduction from [5], the XMSS-T security reduction from [11], and a new security analysis for
multi-instance FORS.

In our technical speciﬁcation of SPHINCS+ we used the abstraction of tweakable hash
functions to allow for diﬀerent ways of keying a function and generating bitmasks.
In the
security reduction we will remove this abstraction and assume that each call to the hash
function used to instantiate the tweakable hash is keyed with a diﬀerent value and inputs

47

are XORed with a bitmask before being processed. Moreover, we assume that the bitmasks
are generated using a third PRF called PRFBM. The PRF PRFBM is the single function
assumed to behave like a random oracle. Finally, we make a statistical assumption on the
hash function F. Informally we require that every element in the image of F has at least two
preimages, i.e.,

(∀k ∈ {0, 1}n)(∀y ∈ IMG(Fk))(∃x, x(cid:48) ∈ {0, 1}n) : x (cid:54)= x(cid:48) ∧ Fk(x) = fk(x(cid:48)).

(14)

Informally, we will prove the following Theorem where F, H, and T are the cryptographic

hash functions used to instantiate F and H, T respectively.

Theorem 9.1 For security parameter n ∈ N, parameters w, h, d, m, t, k as described above,
SPHINCS+ is existentially unforgeable under post-quantum adaptive chosen message attacks
if

• F, H, and T are post-quantum distinct-function multi-target second-preimage resistant

function families,

• F fulﬁlls the requirement of Eqn. 14,

• PRF, PRFmsg are post-quantum pseudorandom function families,

• PRFBM is modeled as a quantum-accessible random oracle, and

• Hmsg is a post-quantum interleaved target subset resilient hash function family.

More speciﬁcally, the insecurity function InSecPQ-EU-CMA (cid:0)SPHINCS+; ξ, 2h(cid:1) describing the
maximum success probability over all adversaries running in time ≤ ξ against the PQ-EU-CMA
security of SPHINCS+ is bounded by

InSecpq-eu-cma (cid:0)SPHINCS+; ξ(cid:1) ≤ 2(InSecpq-prf (PRF; ξ) + InSecpq-prf (PRFmsg; ξ)
+ InSecpq-itsr (Hmsg; ξ) + InSecpq-dm-spr (F; ξ) + InSecpq-dm-spr (H; ξ) + InSecpq-dm-spr (T; ξ))

(15)

9.1. Preliminaries

Before we start with the proof, we have to provide two deﬁnitions. In general, we refer the
reader to [11] for formal deﬁnitions of the above properties with two exceptions. First, we
use a variant of post-quantum multi-function multi-target second-preimage resistance called
post-quantum distinct-function multi-target second-preimage resistance. The distinction here
is that the targets are given for distinct but predeﬁned functions from the family while for
the multi-function notion, the functions are sampled together with the target, uniformly at
random.

Second, we deﬁne a variant of subset-resilience which captures the use of FORS in SPHINCS+
which we call (post-quantum) interleaved target subset resilience. The idea is that from a the-
oretical point of view, one can think of the 2h FORS instances as a single huge HORS-style
signature scheme. The secret key consists of 2h key-sets which in turn consist of k key-subsets
of t secret n-byte values, each. The message digest function Hmsg maps a message to a key-set
(by outputting the index) and a set of indexes such that each index is used to select one secret
value per key-subset of the selected key-set.

Formally, the security of this multi-instance FORS boils down to the inability of an adversary

48

• to learn actual secret values which were not disclosed before,

• to replace secret values by values of its choosing, and

• to ﬁnd a message which is mapped to a key-set and a set of indexes such that the
adversary has already seen the secret values indicated by the indexes for that key-set.

The former two points will be shown to follow from the properties of F, H, and T as well
as those of PRF. The latter point is exactly what (post-quantum) interleaved target subset
resilience captures.

We deﬁne those properties in the following.

Post-quantum distinct-function, multi-target second-preimage resistance (pq-dm-spr).
In the following let λ ∈ N be the security parameter, α = poly(λ), κ = poly(λ), and
Hλ = {HK : {0, 1}α → {0, 1}λ}K∈{0,1}κ be a family of functions. We deﬁne the success
probability of any (quantum) adversary A against pq-mm-spr. This deﬁnition is parameter-
ized by the number of targets

Succ

pq-dm-spr
Hλ,p

(A) = Pr [ (∀ {Ki}q

1 ⊂ ({0, 1}κ)q), Mi

$← {0, 1}α, 0 < i ≤ p;

(j, M (cid:48)) $← A((K1, M1), . . . , (Kp, Mp)) :
M (cid:48) (cid:54)= Mj ∧ HKj (Mj) = HKj (M (cid:48))(cid:3) .

(16)

(Post-quantum) interleaved target subset resilience. In the following let λ ∈ N be the
security parameter, α = poly(λ), κ = poly(λ), and Hλ = {HK : {0, 1}α → {0, 1}λ}K∈{0,1}κ
be a family of functions. Further consider the mapping function MAPh,k,t
: {0, 1}λ →
{0, 1}h × [0, t − 1]k which for parameters h, k, t maps an λ-bit string to a set of k indexes
((I, 1, J1), . . . , (I, k, Jk)) where I is chosen from [0, 2h − 1] and each Ji is chosen from [0, t − 1].
Note that the same I is used for all tuples (I, i, Ji).

We deﬁne the success probability of any (quantum) adversary A against pq-mm-spr of Hλ.
Let G = MAPh,k,t ◦ Hλ. This deﬁnition uses an oracle O(·) which upon input of a α-bit
$← {0, 1}κ and returns Ki and G(Ki, Mi). The adversary may
message Mi samples a key Ki
query this oracle with messages of its choosing. The adversary would like to ﬁnd another G
input whose output is covered by the G outputs produced by the oracle, without the input
being one of the inputs used by the oracle. Note that the adversary knows the description of
G and can evaluate it on randomizer-message pairs of its choosing. However, these queries do
not count into the set of values which need to cover the adversary’s output.

Succpq-itsr
H,q

(A) = Pr

(cid:104)

(K, M ) ← AO(·)(1λ)

s.t. G(K, M ) ⊆

q
(cid:91)

j=1

G(Kj, Mj)

∧ (K, M ) (cid:54)∈ {(Kj, Mj)}q
1

(cid:105)

where q denotes the number of oracle queries of A and the pairs {(Kj, Mj)}q
1
responses of oracle O.

represent the

Note that this is actually a strengthening of (post-quantum) target subset resilience in the
multi-target setting.
In the multi-target version of target subset resilience, A was able to
freely choose the common index I for its output. In interleaved target subset resilience, I is
determined by G and input M .

49

9.2. Security Reduction

The security reduction is essentially an application of techniques used especially in [11]. Hence,
we will only roughly sketch it here.

We want to bound the success probability of an adversary A against the PQ-EU-CMA se-
curity of SPHINCS+. We start with GAME.0 which is the original PQ-EU-CMA game.
Now consider a second game GAME.1 where all outputs of PRF are replaced by truly
random values. The diﬀerence in success probability of any forger A must be bound by
InSecpq-prf (PRF; ξ) otherwise we could use A to break the pseudorandomness of PRF with
a success probability greater InSecpq-prf (PRF; ξ) which would contradict the deﬁnition of
InSecpq-prf (PRF; ξ).

Next, consider a game GAME.2 which is the same as GAME.1 but all outputs of PRFmsg
are replaced by truly random values. Following the same reasoning as above, the diﬀerence
in success probability of any adversary A playing in the two games must be bounded by
InSecpq-prf (PRFmsg; ξ).

Next, we consider GAME.3 where we consider the game lost if A outputs a valid forgery
(M, SIG) where the FORS signature part of SIG diﬀers from the signature which would be
obtained by signing M with the secret key of the challenger. The diﬀerence of any A in
winning the two games must be bounded by InSecpq-dm-spr (F; ξ) + InSecpq-dm-spr (H; ξ) +
InSecpq-dm-spr (T; ξ). Otherwise, we could use A to break the post-quantum distinct-function,
multi-target second-preimage resistance of F, H, or T. A detailed proof of this follows exactly
along the lines of the security reduction for XMSS-T in [11]. Given distinct challenges for
each call to F, H or T for the key-set deﬁned by PK.seed and the address space, we program
PRFBM to output bitmasks which are the XOR of the input to the according tweakable hash
function and the given challenge. That way we program the actual input to the hash function
to be the challenge value. This allows us to extract a second preimage if a collision happens
between the forgery and the honestly generated signature. A pigeon hole argument can be
used to show that such a collision must exist in this case.

Next, we consider GAME.4 which diﬀers from GAME.3 in that we are considering the game
lost if an adversary outputs a valid forgery (M, SIG) where the FORS signature part of SIG
contains a secret value which is the same as that of an honestly generated signature of M but
was not contained in any of the signatures obtained by A via the singing oracle. The diﬀerence
of any (unbounded) A in the two games is bounded by 1/2 times the success probability of
A in GAME.3. The reason is that the secret values which were not disclosed to A before still
contain 1 bit of entropy, even for an unbounded A.

Finally, we have to bound the success probability of A in GAME.4. But GAME.4 can
be viewed as the (post-quantum) interleaved target subset resilience game. Because, if A
returns a valid signature and succeeds in the GAME, the FORS signature must be valid and
consist only of values that have been observed by A in previous signatures. Hence, the success
probability of A in GAME.4 is bounded by InSecpq-itsr (Hmsg; ξ) per deﬁnition.

Putting things together we obtain the claimed bound.

(cid:3)

9.3. Security Level / Security Against Generic Attacks

As shown in Theorem 9.1, the security of SPHINCS+ relies on the properties of the functions
used to instantiate all the cryptographic function families (and the way they are used to
In the following we assume that there do not exist any
instantiate the function families).

50

structural attacks against the used functions SHA2, SHAKE, and Haraka. In later sections
we justify this assumption for each of the function familes.

For now, we only consider generic attacks. We now consider generic classical and quantum
attacks against distinct-function multi-target second-preimage resistance, pseudorandomness
(of function families), and interleaved target subset resilience. Runtime of adversaries is
counted in terms of calls to the cryptographic function families.

9.3.1. Distinct-Function Multi-Target Second-Preimage Resistance

To evaluate the complexity of generic attacks against hash function properties the hash func-
tions are commonly modeled as (family of) random functions. Note, that for random functions
there is no diﬀerence between distinct-function multi-target second-preimage resistance and
multi-function multi-target second-preimage resistance. Every key just selects a new ran-
dom function, independent of the key being random or not. In [11] it was shown that the
success probability of any classical qhash-query adversary against multi-function multi-target
second-preimage resistance of a random function with range {0, 1}8n (and hence also against
distinct-function multi-target second-preimage resistance) is exactly qhash+1
. For qhash-query
quantum adversaries the success probability is Θ( (qhash+1)2
). Note that these bounds are
independent of the number of targets.

28n

28n

9.3.2. Pseudorandomness of Function Families

The best generic attack against the pseudorandomness of a function family is commonly
believed to be exhaustive key search. Hence, for a function family with key space {0, 1}8n
the success probability of a classical adversary that evaluates the function family on qkey keys
is again bounded by qkey+1
28n . For qkey-query quantum adversaries the success probability of
exhaustive search in an unstructured space with {0, 1}8n elements is Θ( (qkey+1)2
) as implicitly
shown in [11] (just consider this as preimage search of a random function).

28n

9.3.3. Interleaved Target Subset Resilience

To evaluate the attack complexity of generic attacks against interleaved target subset resilience
we again assume that the used hash function family is a family of random functions.

Recall that there are parameters h, k, t where t = 2a. These parameters deﬁne the fol-
lowing process of choosing sets: generate independent uniform random integers I, J1, . . . , Jk,
where I is chosen from [0, 2h − 1] and each Ji is chosen from [0, t − 1]; then deﬁne S =
{(I, 1, J1), (I, 2, J2), . . . , (I, k, Jk)}. (In the context of SPHINCS+, S is a set of positions of
FORS private key values revealed in a signature: I selects the FORS instance, and Ji selects
the position of the value revealed from the ith set inside this FORS instance.)

The core combinatorial question here is the probability that S0 ⊂ S1 ∪ · · · ∪ Sq, where each
Si is generated independently by the above process. (In the context of SPHINCS+, this is the
probability that a new message digest selects FORS positions that are covered by the positions
already revealed in q signatures.) Write Sα as {(Iα, 1, Jα,1), (Iα, 2, Jα,2), . . . , (Iα, k, Jα,k)}.

For each α, the event Iα = I0 occurs with probability 1/2h, and these events are independent.
Consequently, for each γ ∈ {0, 1, . . . , q}, the number of indices α ∈ {1, 2, . . . , q} such that
Iα = I0 is γ with probability (cid:0)q

(cid:1)(1 − 1/2h)q−γ/2hγ.

γ

51

Deﬁne DarkSideγ as the conditional probability that (I0, i, J0,i) ∈ S1 ∪ · · · ∪ Sq, given
In other words, 1 − DarkSideγ is the conditional probability
that the above number is γ.
that (I0, i, J0,i) /∈ {(I1, i, J1,i), (I2, i, J2,i), . . . , (Iq, i, Jq,i)}. There are exactly γ choices of α ∈
{1, 2, . . . , q} for which Iα = I0, and each of these has probability 1 − 1/t of Jα,i missing J0,i.
These probabilities are independent, so 1 − DarkSideγ = (1 − 1/t)γ.

The conditional probability that S0 ⊂ S1 ∪ · · · ∪ Sq, again given that the above number is
γ, is the kth power of the DarkSideγ quantity deﬁned above. Hence the total probability (cid:15)
that S0 ⊂ S1 ∪ · · · ∪ Sq is

DarkSidek
γ

(cid:88)

γ

(cid:19) (cid:18)

1 −

(cid:18)q
γ

1
2h

(cid:19)q−γ 1

2hγ =

(cid:18)

(cid:88)

(cid:18)

1 −

1 −

γ

(cid:19)γ(cid:19)k (cid:18)q
γ

1
t

(cid:19) (cid:18)

1 −

1
2h

(cid:19)q−γ 1

2hγ .

For example, if t = 214, k = 22, h = 64, and q = 264, then (cid:15) ≈ 2−256.01 (with most of
the sum coming from γ between 7 and 13). The set S0 thus has probability 2−256.01 of being
covered by 264 sets S1, . . . , Sq. (In the SPHINCS+ context, a message digest chosen by the
attacker has probability 2−256.01 of selecting positions covered by 264 previous signatures.)

Hence, for any classical adversary which makes qhash queries to function family Hn the

success probability is

(qhash + 1)

(cid:18)

(cid:88)

(cid:18)

1 −

1 −

γ

(cid:19)γ(cid:19)k (cid:18)q
γ

1
t

(cid:19) (cid:18)

1 −

1
2h

(cid:19)q−γ 1

2hγ .

As this for random Hn is search in unstructured data, the best a quantum adversary can do
is Grover search. This leads to a success probability of

(cid:32)
(qhash + 1)2 (cid:88)

(cid:18)

O

(cid:18)

1 −

1 −

γ

(cid:19)γ(cid:19)k (cid:18)q
γ

1
t

(cid:19) (cid:18)

1 −

1
2h

(cid:19)q−γ 1
2hγ

(cid:33)

.

For computations, note that the O is small, and that (1−1/t)γ is well approximated by 1−γ/t.

9.3.4. Security Level of a Given Parameter Set

If we take the above success probabilities for generic attacks and plug them into Theorem 9.1
we get a bound on the success probability of SPHINCS+ against generic attacks of classical
and quantum adversaries. Let q denote the number of adversarial signature queries. For
classical adversaries that make no more than qhash queries to the cryptographic hash function
used, this leads to

InSeceu-cma (cid:0)SPHINCS+; qhash

(cid:1) ≤ 2(

qhash + 1
28n
+ InSecpq-itsr (Hmsg; qhash) +
(cid:18)

= 10

qhash + 1
28n

+ 2(qhash + 1)

+

qhash + 1
28n

qhash + 1
28n
(cid:18)

1 −

1 −

+

qhash + 1
28n
(cid:19)γ(cid:19)k (cid:18)q
γ

1
t

+

(cid:19) (cid:18)

qhash + 1
28n
1
2h

1 −

)

(cid:19)q−γ 1
2hγ

(cid:32)

= O

qhash
28n + (qhash)

(cid:88)

γ

(cid:88)

γ

(cid:18)

(cid:18)

1 −

1 −

(cid:19)γ(cid:19)k (cid:18)q
γ

1
t

(cid:19) (cid:18)

1 −

1
2h

(cid:19)q−γ 1
2hγ

(cid:33)

.

(17)

52

Similarly, for quantum adversaries that make no more than qhash queries to the cryptographic

hash function used, this leads to

InSecpq-eu-cma (cid:0)SPHINCS+; qhash

(cid:1) ≤ 2(

+ InSecpq-itsr (Hmsg; qhash) +

= 10

(qhash + 1)2
28n

+ O

(cid:32)

2(qhash + 1)2 (cid:88)

(cid:32)

= O

(qhash)2
28n + 2(qhash)2 (cid:88)

γ

(qhash + 1)2
28n
(qhash + 1)2
28n
(cid:19)γ(cid:19)k (cid:18)q
γ

1
t

(qhash + 1)2
28n
(qhash + 1)2
28n
(cid:18)

(cid:18)

+

+

1 −

1 −

(cid:18)

γ
(cid:18)

1 −

1 −

)

+

(cid:19) (cid:18)

(qhash + 1)2
28n
1
2h

1 −

(cid:33)

(cid:19)q−γ 1
2hγ
(cid:33)

(cid:19)γ(cid:19)k (cid:18)q
γ

1
t

(cid:19) (cid:18)

1 −

1
2h

(cid:19)q−γ 1
2hγ

.

(18)

To compute the security level also known as bit security one sets this bound on the success

probability to equal 1 and solves for qhash.

9.4. Implementation Security and Side-Channel Protection

Timing attacks. Typical implementations of SPHINCS+ are naturally free of any secret-
dependent branches or secretly indexed loads or stores. SPHINCS+ implementations are
thus free of the two most notorious sources of timing variation. An exception is potentially
SPHINCS+-Haraka, because Haraka is based on AES, which is well known to exhibit timing
vulnerabilities in software implementations [4, 19, 6, 18]. Clearly, SPHINCS+-Haraka should
only be used in environments that support AES in hardware (like almost all modern 64-bit
Intel and AMD and many ARMv8a processors). On some processors also certain arithmetic
instructions do not run in constant time; examples are division instructions on Intel proces-
sors and the UMULL multiplication instruction on ARM Cortex-M3 proceesors. Again, typical
implementations of SPHINCS+ naturally do not use these instructions with secret data as
input – secret data is only processed by symmetric cryptographic primitives that are designed
to not make use of such potentially dangerous arithmetic.

Diﬀerential and fault attacks. We expect that any implementation of SPHINCS+ without
dedicated protection against diﬀerential power or electromagnetic radiation (EM) attacks or
against fault-injection attacks will be vulnerable to such attacks. Deployment scenarios of
SPHINCS+ in which an attacker is assumed to have the power to mount such attacks re-
quire specially protected implementations. For protection against diﬀerential attacks this will
typically require masking of the symmetric primitives; for protection against fault-injection
attacks countermeasures on the hardware level. One additional line of defense against such
advanced implementation attacks is included in the speciﬁcation of SPHINCS+, namely the
option to randomize the signing procedure via the value OptRand (see Subsection 8.1.5).

9.5. Security of SPHINCS+-SHAKE

NIST has standardized several applications of the Keccak permutation, such as the SHA3-256
hash function and the SHAKE256 extendable-output function, after a multi-year Crypto-
graphic Hash Algorithm Competition involving extensive public input. All of these standard-
ized Keccak applications have a healthy security margin against all attacks known.

53

Discussions of the theory of cryptographic hash functions typically identify a few important
properties such as collision resistance, preimage resistance, and second-preimage resistance;
and sometimes include a few natural variants of the attack model such as multi-target attacks
and quantum attacks.
It is important to understand that cryptanalysts engage in a much
broader search for any sort of behavior that is feasible to detect and arguably “non-random”.
NIST’s call for SHA-3 submissions highlighted preimage resistance etc. but then stated the
following:

Hash algorithms will be evaluated against attacks or observations that may threaten
existing or proposed applications, or demonstrate some fundamental ﬂaw in the
design, such as exhibiting nonrandom behavior and failing statistical tests.

It is, for example, non-controversial to use Keccak with a partly secret input as a PRF: any
attack against such a PRF would be a tremendous advance in SHA-3 cryptanalysis, even
though the security of such a PRF is not implied by properties such as preimage resistance.
Similarly, a faster-than-generic attack against the interleaved-target-subset-resilience property,
being able to ﬁnd an input with various patterns of output bits, would be a tremendous
advance.

The particular function SHAKE256 used in SPHINCS+-SHAKE has an internal “capacity”
of 512 bits. There are various attack strategies that search for 512-bit internal collisions,
but this is not a problem even at the highest security category that we aim for. There is
also progress towards showing the hardness of generic quantum attacks against the sponge
construction. Of course, second-preimage resistance is limited by the n-byte output length
that we use.

9.6. Security of SPHINCS+-SHA2

NIST’s SHA-2 family has been standardized for many more years than SHA-3. The stan-
dardization and popularity of SHA-2 mean that these functions are attractive targets for
cryptanalysts, but this has not produced any attacks of concern: each of the members of this
family has a comfortable security margin against all known attacks.

The broad cryptanalytic goal of ﬁnding non-random behavior (see above) is not a new
feature of SHA-3. For example, the security analysis of the popular HMAC-SHA-256 message-
authentication code is based on the security analysis of NMAC-SHA-256, which in turn is based
on a pseudorandomness assumption for SHA-256.

The particular function SHA2-256 used in SPHINCS+-SHA2 has a “chaining value” of only
256 bits, making it slightly weaker in some metrics than SHAKE256 with 256-bit output.
Therefore we make use of SHA2-512 in some cases to achieve the target security level.

9.7. Security of SPHINCS+-Haraka

Both Haraka-256 and Haraka-512 provide a (second)-preimage resistance of 256-bit in the
pre-quantum setting and the best known quantum attack is Grover’s search on 256-bit. How-
ever, the sponge construction we use for HarakaS has a capacity of 256-bit which allows at
most security level 2. The best attack breaking any of the security properties required for
SPHINCS+ is a preimage attack which corresponds to a collision search on 256-bit for the
sponge construction we use. Instances with larger output size are limited by this and provide
a less eﬃcient trade-oﬀ between security and eﬃciency.

54

Another aspect is that we pseudo-randomly generate round constants derived from a seed.
An attacker cannot inﬂuence the values of the constants for one instance, but can search for
instances having weak constants. As shown by Jean [13], a weak choice of round constants
can lead to more eﬃcient preimage attacks. In general, a bad choice of round constants does
not break the symmetry of a single round.
In the case of Haraka, which combines several
calls of two rounds of AES-128 per round to create bigger blocks, the round constants have
to break the symmetry within two rounds of AES, but also between the diﬀerent calls of the
two rounds. Let us ﬁrst focus on Haraka-256.

To break the symmetry within one round of AES, we require that the value of the round con-
stant is not the same for each column. For round constants generated via an extendable-output
function from a random 256-bit seed, we consider this event to happen with a probability of
2−96. Moreover, that the symmetry of two rounds of AES is not broken by round-constants
happens with 2−192. In other words, since one instance of Haraka-256 uses 10 times 2-round
AES, only for a fraction of 10 · 2−192 instances/keys, we expect that the symmetry within one
call of 2 rounds of AES is not broken. Even if this happens, all other 2 round AES calls used
in Haraka-256 have with a high probability constants that break the symmetry of 2 rounds of
AES for all other calls. Hence, we do not expect any negative consequences for the security.
Haraka-256 processes two 2-round AES-calls in parallel per round. So, we also do not want
to have the same round constants in these calls. This condition happens with probability
5 · 2−256. Furthermore, the probability that two rounds have the same round constants is
10 · 2−512. Similar observations are also valid for Haraka-512. Hence, we conclude that it is
very unlikely, that a pseudo-random generation of the round constants per instance leads to
weak round constants.

10. Performance

In order to obtain benchmarks, we evaluate our reference implementation on a machine using
the Intel x86-64 instruction set. In particular, we use a single core of a 3.1 GHz Intel Xeon
E3-1220 CPU (Haswell). We follow the standard practice of disabling TurboBoost and hyper-
threading. Furthermore, it has 32GiB of RAM and the system ran on Ubuntu 18.04 with Linux
kernel is 4.15.0-96-generic. We compiled the code using gcc (Ubuntu 7.5.0-3ubuntu1 18.04)
7.5.0, with the compiler optimization ﬂag -O3.

10.1. Runtime

For the deﬁned parameter sets, the resulting cycle counts are listed in Table 4.

We also provide optimized implementations for platforms supporting the AVX2 instruction
set. For Haraka, it is especially relevant to also examine platforms that have the AES-NI
instruction set available. We used the same system as described above, this time including the
march=native compiler ﬂag, as well as flto and fomit-frame-pointer. Performance results
are listed in Table 5, Table 6 and Table 7.

10.2. Space

In Table 8, we list the key and signature sizes (in bytes) for the deﬁned parameter sets. In
terms of memory consumption, we remark that the reference implementation tends towards
low stack usage. This shows for example in procedures such as computing authentication

55

SPHINCS+-SHAKE-128s-simple
SPHINCS+-SHAKE-128s-robust
SPHINCS+-SHAKE-128f-simple
SPHINCS+-SHAKE-128f-robust
SPHINCS+-SHAKE-192s-simple
SPHINCS+-SHAKE-192s-robust
SPHINCS+-SHAKE-192f-simple
SPHINCS+-SHAKE-192f-robust
SPHINCS+-SHAKE-256s-simple
SPHINCS+-SHAKE-256s-robust
SPHINCS+-SHAKE-256f-simple
SPHINCS+-SHAKE-256f-robust

SPHINCS+-SHA2-128s-simple
SPHINCS+-SHA2-128s-robust
SPHINCS+-SHA2-128f-simple
SPHINCS+-SHA2-128f-robust
SPHINCS+-SHA2-192s-simple
SPHINCS+-SHA2-192s-robust
SPHINCS+-SHA2-192f-simple
SPHINCS+-SHA2-192f-robust
SPHINCS+-SHA2-256s-simple
SPHINCS+-SHA2-256s-robust
SPHINCS+-SHA2-256f-simple
SPHINCS+-SHA2-256f-robust

SPHINCS+-Haraka-128s-simple
SPHINCS+-Haraka-128s-robust
SPHINCS+-Haraka-128f-simple
SPHINCS+-Haraka-128f-robust
SPHINCS+-Haraka-192f-simple
SPHINCS+-Haraka-192f-robust
SPHINCS+-Haraka-192s-simple
SPHINCS+-Haraka-192s-robust
SPHINCS+-Haraka-256f-simple
SPHINCS+-Haraka-256f-robust
SPHINCS+-Haraka-256s-simple
SPHINCS+-Haraka-256s-robust

key generation

signing

veriﬁcation

616 484 336
1 195 409 786
9 649 130
18 726 982
898 362 434
1 753 646 932
14 215 518
27 463 376
594 081 566
1 156 363 648
36 950 136
72 503 094

358 061 994
714 027 022
5 590 602
11 135 058
524 116 024
1 059 562 738
8 227 944
16 581 076
346 844 762
1 006 905 074
21 763 590
62 599 672

576 421 410
1 095 155 240
9 137 070
17 119 708
13 399 816
25 376 582
857 570 254
1 625 369 822
35 650 224
67 266 388
569 851 046
1 077 657 774

4 682 570 992
8 995 481 640
239 793 806
460 757 304
8 091 419 556
15 306 007 790
386 861 992
734 072 042
7 085 272 100
13 198 544 260
763 942 250
1 467 095 732

2 721 595 944
5 363 065 742
138 610 500
273 836 364
5 012 149 284
9 893 267 932
232 973 880
458 983 816
4 499 800 456
12 382 647 014
468 188 036
1 312 473 846

4 594 239 682
8 555 157 606
232 172 172
430 223 622
392 561 468
736 487 034
8 710 115 544
16 200 067 256
832 534 808
1 519 602 658
8 717 853 894
15 566 614 908

4 764 084
9 232 084
12 909 924
28 152 828
6 465 506
13 509 022
19 876 926
39 295 686
10 216 560
19 292 734
19 886 032
39 555 542

2 712 044
5 561 194
7 757 942
16 101 098
4 333 066
8 557 224
11 768 382
24 679 894
6 060 438
18 784 558
11 934 164
37 139 082

5 211 916
9 749 770
13 148 448
27 216 072
20 424 354
40 090 578
7 572 424
15 276 532
22 061 746
42 244 366
11 740 252
22 516 650

Table 4: Runtime benchmarks for SPHINCS+

56

SPHINCS+-Haraka-128s-simple
SPHINCS+-Haraka-128s-robust
SPHINCS+-Haraka-128f-simple
SPHINCS+-Haraka-128f-robust
SPHINCS+-Haraka-192s-simple
SPHINCS+-Haraka-192s-robust
SPHINCS+-Haraka-192f-simple
SPHINCS+-Haraka-192f-robust
SPHINCS+-Haraka-256s-simple
SPHINCS+-Haraka-256s-robust
SPHINCS+-Haraka-256f-simple
SPHINCS+-Haraka-256f-robust

key generation

signing

veriﬁcation

30 075 604
37 113 806
482 332
587 548
46 369 950
63 387 838
732 770
998 446
28 822 310
40 954 800
1 809 078
2 599 368

240 763 926
304 905 780
12 196 792
15 176 760
481 682 614
718 896 354
21 433 286
30 866 288
451 164 660
677 039 436
41 973 226
61 706 762

308 774
432 066
799 808
1 072 774
480 264
759 952
1 205 698
1 799 300
696 980
1 046 096
1 252 598
1 854 540

Table 5: Runtime benchmarks for SPHINCS+-Haraka on AES-NI

SPHINCS+-SHA2-128s-simple
SPHINCS+-SHA2-128s-robust
SPHINCS+-SHA2-128f-simple
SPHINCS+-SHA2-128f-robust
SPHINCS+-SHA2-192s-simple
SPHINCS+-SHA2-192s-robust
SPHINCS+-SHA2-192f-simple
SPHINCS+-SHA2-192f-robust
SPHINCS+-SHA2-256s-simple
SPHINCS+-SHA2-256s-robust
SPHINCS+-SHA2-256f-simple
SPHINCS+-SHA2-256f-robust

key generation

signing

veriﬁcation

84 964 790
175 257 460
1 334 220
2 748 026
125 310 788
260 903 972
1 928 970
4 063 066
80 943 202
339 101 780
5 067 546
21 327 470

644 740 090
1 328 848 352
33 651 546
68 541 846
1 246 378 060
2 517 396 082
55 320 742
113 484 456
1 025 721 040
3 912 132 754
109 104 452
435 984 168

861 478
1 827 104
2 150 290
4 801 338
1 444 030
3 103 732
3 492 210
7 552 358
1 986 974
8 294 732
3 559 052
14 938 510

Table 6: Runtime benchmarks for SPHINCS+-SHA2 on AVX2

57

SPHINCS+-SHAKE-128s-simple
SPHINCS+-SHAKE-128s-robust
SPHINCS+-SHAKE-128f-simple
SPHINCS+-SHAKE-128f-robust
SPHINCS+-SHAKE-192s-simple
SPHINCS+-SHAKE-192s-robust
SPHINCS+-SHAKE-192f-simple
SPHINCS+-SHAKE-192f-robust
SPHINCS+-SHAKE-256s-simple
SPHINCS+-SHAKE-256s-robust
SPHINCS+-SHAKE-256f-simple
SPHINCS+-SHAKE-256f-robust

key generation

signing

veriﬁcation

143 900 796
274 483 474
2 249 444
4 272 402
206 105 502
397 548 084
3 220 902
6 175 702
136 190 230
258 430 892
8 535 534
16 296 098

1 102 470 520
2 076 548 104
56 933 788
106 032 762
1 910 461 606
3 549 895 260
89 875 552
167 173 520
1 650 717 926
2 982 404 428
176 951 378
329 696 258

1 189 102
2 408 782
3 346 068
6 677 094
1 653 314
3 300 788
4 783 424
9 330 848
2 559 892
4 669 406
5 030 988
9 716 888

Table 7: Runtime benchmarks for SPHINCS+-SHAKE on AVX2

SPHINCS+-128s
SPHINCS+-128f
SPHINCS+-192s
SPHINCS+-192f
SPHINCS+-256s
SPHINCS+-256f

public key size

secret key size

signature size

32
32
48
48
64
64

64
64
96
96
128
128

7 856
17 088
16 224
35 664
29 792
49 856

Table 8: Key and signature sizes in bytes

paths and tree roots, which is done using the treehash algorithm (which requires stack usage
linear in the tree height, rather than the naive exponential approach of ﬁrst computing the
entire tree and then cherry-picking the relevant nodes).

11. Advantages and Limitations

The advantages and limitations of SPHINCS+ can be summarized in one sentence: On the
one hand, SPHINCS+ is probably the most conservative design of a post-quantum signature
scheme, on the other hand, it is rather ineﬃcient in terms of signature size and speed. In the
following we discuss disadvantages and advantages in some more detail.

Disadvantage: Signature size and speed. The clear drawback of SPHINCS+ is signing
speed and signature size. SPHINCS+ is clearly not competing to be the smallest or fastest
signature scheme. However, as shown in Section 7.1.1 there exists a magnitude of possible
trade-oﬀs allowing to tweak SPHINCS+ as long as one can tolerate at least one of the two,
i.e., somewhat slow signing or somewhat large signatures.

Advantage: “Minimal Security Assumptions”. In contrast to other post-quantum crypto

58

schemes (including signatures as well as public-key encryption schemes), SPHINCS+ does not
introduce a new intractability assumption. The security of SPHINCS+ is solely based on
assumptions about the used hash function. A secure hash function is required by any eﬃcient
signature scheme that supports arbitrary input lengths.

Moreover, a collision attack against the hash function does not suﬃce to break the security
of SPHINCS+. We consider this an important feature given the successful collision attacks
on MD5 and SHA1. Especially given that even for MD5 second-preimage resistance has not
been broken, yet.

Finally, the cryptographic community has a good understanding of (exact) hash-function
security, especially after the recent SHA3 competition. This is in contrast to the relatively
new problems used in other areas of post-quantum cryptography. Even though some of those
problems are known already for a long time, estimating the hardness of solving speciﬁc problem
instances is far less understood.

Advantage: State-of-the-art attacks are easily analyzed. The most eﬃcient attacks
known against SPHINCS+ are easy to state and analyze, such as searching for a hash input
that has a particular pattern of output bits. The analogous quantum attacks are also easy to
state and analyze, such as using Grover’s algorithm to accelerate the same search. This allows
precise quantiﬁcation of the security levels provided by SPHINCS+.

Advantage: Small key sizes. Another advantage of SPHINCS+ is the small size of the keys,
in particular the public-key size. In many applications public keys are transmitted frequently;
almost as frequently as signatures. This is typically the case for certiﬁcates (or certiﬁcate
chains) as used, for example, in TLS.

Advantage: Overlap with XMSS. One more feature of SPHINCS+ is the large overlap
with the stateful hash-based signature scheme XMSS. Especially the veriﬁcation code of XMSS
is almost entirely contained within the SPHINCS+ veriﬁcation code. Hence, in scenarios like
virtual private networks where clients authenticate towards a gateway using signatures it is
easy to combine these two. While every client that actually can support to handle a state
can use XMSS, every other client can use SPHINCS+. Only the gateway has to support
veriﬁcation of both, XMSS and SPHINCS+ signatures. This becomes especially interesting
as SPHINCS+ is not particularly well suited for resource-constrained devices (although it was
shown that it is in principle possible to implement SPHINCS+ on such devices [10]). However,
most resource-constrained devices can deal with a state and XMSS is far better suited for these
devices.

Advantage: Reuse of established building blocks. SPHINCS+ uses the basic hash func-
tion as building block many times. Any speedup to implementations of SHA-256, SHAKE256
or Haraka directly beneﬁts the SPHINCS+ speed. In particular hardware support for hash
functions in the CPU, cryptographic coprocessors, or via instruction-set extensions instantly
leads to faster SPHINCS+ signatures (or to smaller SPHINCS+ signatures via tuning w).

12. Acknowledgements

The authors would like to thank Wouter Boschmann, Andrew Bulychev, Lena Heimberger,
Hassan Mohseni, Joost Renes, Christine van Vredendaal, and Auke Zeilstra for feedback on
this proposal.

59

References

[1] Jean-Philippe Aumasson and Guillaume Endignoux. Clarifying the subset-resilience prob-
lem. Cryptology ePrint Archive, Report 2017/909, 2017. https://eprint.iacr.org/
2017/909. 44

[2] Jean-Philippe Aumasson and Guillaume Endignoux. Improving stateless hash-based sig-
natures. Cryptology ePrint Archive, Report 2017/933, 2017. https://eprint.iacr.
org/2017/933. 44

[3] D. J. Bernstein, A. Hülsing, S. Kölbl, R. Niederhagen, J. Rijneveld, and P. Schwabe.
The SPHINCS+ Signature Framework.
In 2019 ACM SIGSAC Conference on Com-
puter and Communications Security (CCS ’19). ACM, New York, NY, USA, 2019.
https://doi.org/10.1145/3319535.3363229. 47

[4] Daniel J. Bernstein.

ID:
cd9faae9bd5308c440df50fc26a517b4, https://cr.yp.to/papers.html#cachetiming. 53

Cache-timing attacks on AES, 2004.

Document

[5] Daniel J. Bernstein, Daira Hopwood, Andreas Hülsing, Tanja Lange, Ruben Niederha-
gen, Louiza Papachristodoulou, Michael Schneider, Peter Schwabe, and Zooko Wilcox-
O’Hearn. SPHINCS: Practical Stateless Hash-Based Signatures.
In Elisabeth Oswald
and Marc Fischlin, editors, EUROCRYPT 2015, volume 9056 of LNCS, pages 368–397.
Springer Berlin Heidelberg, 2015. 5, 26, 43, 47

[6] Joseph Bonneau and Ilya Mironov. Cache-collision timing attacks against AES. In Louis
Goubin and Mitsuru Matsui, editors, Cryptographic Hardware and Embedded Systems –
CHES 2006, volume 4249 of Lecture Notes in Computer Science, pages 201–215. Springer-
Verlag Berlin Heidelberg, 2006. http://www.jbonneau.com/AES_timing_full.pdf. 53

[7] Johannes Buchmann, Erik Dahmen, and Andreas Hülsing. XMSS - a practical forward
secure signature scheme based on minimal security assumptions. In Bo-Yin Yang, editor,
Post-Quantum Cryptography, volume 7071 of LNCS, pages 117–129. Springer, 2011. 5

[8] A. Hülsing, D. Butin, S.-L. Gazdag, J. Rijneveld, and A. Mohaisen. XMSS: eXtended

Merkle Signature Scheme. RFC 8391, May 2018. 5

[9] Andreas Hülsing. W-OTS+ – shorter signatures for hash-based signature schemes. In Amr
Youssef, Abderrahmane Nitaj, and Aboul-Ella Hassanien, editors, Progress in Cryptology
– AFRICACRYPT 2013, volume 7918 of LNCS, pages 173–188. Springer, 2013. 14, 47

[10] Andreas Hülsing, Joost Rijneveld, and Peter Schwabe. ARMed SPHINCS. In Chen-Mou
Cheng, Kai-Min Chung, Giuseppe Persiano, and Bo-Yin Yang, editors, PKC 2016, volume
9614 of LNCS, pages 446–470, Berlin, Heidelberg, 2016. Springer Berlin Heidelberg. 59

[11] Andreas Hülsing, Joost Rijneveld, and Fang Song. Mitigating multi-target attacks in
hash-based signatures. In Chen-Mou Cheng, Kai-Min Chung, Giuseppe Persiano, and
Bo-Yin Yang, editors, PKC 2016, volume 9614 of LNCS, pages 387–416. Springer Berlin
Heidelberg, Berlin, Heidelberg, 2016. 6, 9, 43, 47, 48, 50, 51

[12] Andreas Hülsing and Mikhail Kudinov. Recovering the tight security proof of sphincs+.
Cryptology ePrint Archive, Report 2022/346, 2022. https://ia.cr/2022/346. 46, 47

60

[13] Jérémy Jean. Cryptanalysis of Haraka. IACR Trans. Symmetric Cryptol., 2016(1):1–12,

2016. 55

[14] Stefan Kölbl, Martin Lauridsen, Florian Mendel, and Christian Rechberger. Haraka v2
– eﬃcient short-input hashing for post-quantum applications. volume 2016, pages 1–29,
2017. 35

[15] Leslie Lamport. Constructing digital signatures from a one way function. Technical

Report SRI-CSL-98, SRI International Computer Science Laboratory, 1979. 5

[16] David McGrew, Michael Curcio, and Scott Fluhrer. Hash-based signatures.

Internet

Draft, IETF Crypto Forum Research Group, 2019. 6, 39, 45

[17] Ralph Merkle. A certiﬁed digital signature.

In Gilles Brassard, editor, Advances in

Cryptology – CRYPTO ’89, volume 435 of LNCS, pages 218–238. Springer, 1990. 5

[18] Michael Neve and Jean-Pierre Seifert. Advances on access-driven cache attacks on AES.
In Eli Biham and Amr M. Youssef, editors, Selected Areas in Cryptography, volume 4356
of Lecture Notes in Computer Science, pages 147–162. Springer-Verlag Berlin Heidelberg,
2007. 53

[19] Dag Arne Osvik, Adi Shamir, and Eran Tromer. Cache attacks and countermeasures: the
case of AES. In David Pointcheval, editor, Topics in Cryptology – CT-RSA 2006, volume
3860 of LNCS, pages 1–20. Springer, 2006. http://eprint.iacr.org/2005/271/. 53

[20] Bart Preneel and Paul C. van Oorschot. MDx-MAC and building fast MACs from hash
functions. In CRYPTO, volume 963 of Lecture Notes in Computer Science, pages 1–14.
Springer, 1995. 41

[21] Leonid Reyzin and Natan Reyzin. Better than BiBa: Short one-time signatures with fast
signing and verifying. In Lynn Batten and Jennifer Seberry, editors, Information Security
and Privacy 2002, volume 2384 of LNCS, pages 1–47. Springer, 2002. 26

61

A. Parameter-evaluation Sage script

tsec,hashbytes = 125,16
#tsec,hashbytes = 192,24
#tsec,hashbytes = 253,32
maxsigs=2**64

F = RealField(100)

def ld(r):

return -F(log(1/F(2**(8*hashbytes))+F(r)) / log2)

def pow(p,e):

return F(p)**e

def qhitprob(qs,r):
p = F(1/leaves)
return binomial(qs,r)*(pow(p,r))*(pow(1-p,qs-r))

def la(m,w):

return ceil(m / log(w,2))

def lb(m,w):

return floor( log(la(m,w)*(w-1), 2) / log(w,2)) + 1

def lc(m,w):

return la(m,w) + lb(m,w)

for h in range(35,74,2):

leaves = 2**h
for b in range(4,17):

for k in range(30,32):

sigma=0
r = 1
while True:
r = F(r)
p = min(1,F((r/F(2**b)))**k)
q = qhitprob(maxsigs,long(r))*p
sigma += q
r += 1
if(r > maxsigs/leaves and q < F(2)**(-10*tsec)): # beyond expected number of collisions and negl. prob, the 10 needs justification!

break

if(sigma<2**-tsec):

for d in range(4,h):

if(h % d == 0 and h <= 64+(h/d)):

for w in [16,256]:

wots = lc(8*hashbytes,w)
sigsize = ((b+1)*k+h+wots*d+1)*hashbytes
if(sigsize < 50000):

print h, # total tree height
print d, # number of tree layers, subtree height is h/d
print b, # height of FORS trees

62

print k, # number of trees for FORS
print w, # Winternitz parameter
print round(ld(sigma)),
print sigsize,
# Speed estimate based on (rough) hash count
print (k*2**(b+1) + d*(2**(h/d)*(wots*w+1)))

63

