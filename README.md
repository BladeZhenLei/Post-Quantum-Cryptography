The rapid development of quantum computers has raised concerns about computer security, especially those public-key cryptographies that are currently in use, which are inevitably subject to quantum attacks, assuming that large scale quantum computers will be built.
\par
While most current symmetric cryptographic algorithms are considered to be relatively secure against attacks by quantum computers, the problem with public-key cryptographies is that they rely heavily on three hard mathematical problems [3]: the integer factorization problem, the discrete logarithm problem, and the elliptic-curve problem.
\par
The Quantum Fourier Transform algorithms can be implemented on quantum computer using only polynomial number of quantum gates, also called the 'quantum killer app', which breaks public-key cryptographies in polynomial time (i.e. Shor's Algorithm against RSA [8], ECC [9] and DSA [6]).
The amplitude amplification based algorithms, allow amplification of a chosen subspace of a quantum state, one such example is the Grover's Algorithm [7], which provides a quadratic speedup attack against symmetric ciphers. However, doubling the key length may prevent such brute force method from succeeding.

The above table (provided by the National Institute of Standards and Technology (NIST) [1]) gives a suggestion of the security levels of current popular cryptographic algorithms at a post-quantum scenario. 
There five major candidates of post-quantum cryptographies, and we will discuss two of them in details.
\end{section}


\begin{section}{Lattice Based Cryptography}
\hspace{5mm}The theory of lattice-based cryptography is essentially hiding a point in a high-dimensional lattice $mod$ $q$ by making small changes to all coordinates, the time complexity to such problem is sub-exponential. 
\par
In the 1990s, Hoffstein, Pipher, and Silverman [11] introduced an encryption system “NTRU” that remains unbroken today. This system works as follows.
\par
The public key is a $p$-coefficient polynomial
\[h= h_0+h_1x+\dots+h_{p-1}x^{p-1},\]
with each coefficient in the set $\{0,1,\dots,q-1\}$. 
A typical choice is $p = 743$ and $q = 2048 = 211$, then the public key has $743\cdot11=8173$ bits.
A ciphertext is another polynomial $c$ in the same range. The sender chooses two secret polynomials $\{d, e\}$ with small (i.e. -1, 0, or 1) coefficients, and computes $c = hd +7e$ $(mod$ $x^p-1$ $(mod$ $q$)). The notation $(mod$ $x^p-1)$ means that $x^p$ is replaced by 1, $x^p+1$ is replaced by $x$, and the notation $(mod$ $q)$ means that each coefficient is replaced by its remainder divided by $q$. 
\par
Define $L$ as the set of pairs $(u, v)$ of $p$-coefficient polynomials with integer coefficients such that
\[hu-v\equiv0\;(mod\;x^p-1\;(mod\;q)),\] 
then $L$ is a lattice in $2p$-dimensional space, and it contains a point close to $(0, c)$, namely $(d, c-e)$. 
The attacker’s problem is therefore finding $\{d, e\}$ given the ciphertext $c$, and the public key $h$ is an example of finding a lattice point close to a given point. 
\par
NTRU secretly generates the public key in a way that makes decoding efficient. 
Specifically, the receiver starts with a short vector of the form $(g, 3f)$, and uses a Euclidean algorithm to find $h$ such that the lattice contains this vector, i.e., such that 
\[hg-3f\equiv0\;(mod\;x^p-1\;(mod\;q)),\] 
then
\[cg\equiv3df+eg\;(mod\;x^p-1\;(mod\;q)).\]
An analysis of coefficient sizes shows that $3df + eg$ almost certainly has all coefficients strictly between $-q/2$ and $q/2$, and then it is easy to find $\{d,e\}$ given $\{f,g\}$.
\par
The NTRU and similar lattice-based cryptosystems have attracted attacks. For example, recent the “cyclotomic” structure [12] of $x^p-1$ has been used to break some lattice-based cryptosystems by an extension of Shor’s algorithm. There are no known attacks exploiting cyclotomics in NTRU, but this attack avenue is new and has not been adequately explored. As another example, recent
attacks [13] that work for arbitrary lattices, without exploiting any polynomial
structure. Much more research is required to gain confidence in the security of lattice-based
cryptographies [3].
\par
Attempts [14] to turn hard lattice problems into signature systems were marred by attacks and surviving systems have suffered from large signature sizes.
The most successful signature systems are based on Lyubashevsky’s signature system [15] from 2012.
The system can be easily presented using integer matrices, usually with polynomial rings and fast Fourier transforms for compact representations. Lyubashevsky’s system uses several parameters, namely integers $\{k, m, n\}$ determining the sizes of matrices, $k$ limiting the Hamming weight of certain vectors, and $q$, a modulus.
Let $A$ be an $n \times m$ integer matrix modulo $q$, this matrix may be shared by all users of the system but can also be chosen individually. The private key is a matrix
$S \in Z_q^{n \times m}$
with entries significantly smaller than $q$, and is restricted to $\{−1,0,1\}$. 
The public key is the
$n \times k$
matrix
$T = AS$,
where the entries are computed modulo $q$. If $A$ is not shared then it is also part of the public key.
\par
The system uses a hash function 
\[H: \mathbb{Z}_q^n\times\{-1,0,1\}^*\rightarrow\{1,0\}^k,\] where the output vectors satisfy that no more than $k$ entries are non-zero. The signer starts by picking $y$ from an $m$-dimensional distribution, typically a discrete
Gaussian distribution, then computes
\[c = H(Ay\;mod \; q, \mu),\]
where $\mu$ is the message,
and $z = Sc + y$. 
The signature is the pair $\{c, z\}$. 
To avoid leaking information about the
private key $S$ through the distribution of $\{c, z\}$, Lyubashevsky uses rejection sampling to force an $S$-independent distribution. This means that the process is restarted with probability depending on $\{c, z\}$.
The signature is accepted as valid if $c$ and $z$ are sufficiently small and if
\[H(Az-Tc \; mod \; q, \mu) = c,\] 
the latter holds for valid signatures because
\[Az-Tc \equiv A(Sc+y)-ASc \equiv Ay \; mod \;q.\]
\par
Proposals such as BLISS [16] improve the running time by reducing the frequency of rejection in the last step, and a ring version with $k=n$ and $m=2n$ signs in under half a millisecond and is verified about 10 times faster.
\par
\begin{figure}[h]
\centering
\includegraphics[scale=0.35] {Fig1.PNG}
	\caption{Perspective view of a $9\times9\times9$ subset of a non-orthogonal 3-dimensional lattice}
	\label{fig1}
\end{figure}
\end{section}


\begin{section}{Supersingular Isogeny}
\hspace{5mm}The number of elements of a finite field is called order, a finite field of order $q$ exists if and only if $q$ is a prime power $p^k$ (where $p$ is a prime number and $k$ is a positive integer). In a field of order $p^k$, adding $p$ copies of any element always results in zero, that is, the characteristic of the field is $p$. An elliptic curve is defined on finite field, either ordinary or supersingular. 
The supersingular case provides easier construction, and all known quantum attacks against
its related computational problems have exponential complexity.
\par
The Supersingular Isogeny Diffie-Hellman (SIDH) protocol [5] works in the quadratic extensions of large prime fields $\mathbb{F}_p$ with $p\equiv3$ $(mod$ $4)$, and the most convenient representation as $\mathbb{F}_{p^2} = \mathbb{F}_p(i)$ with $i^2 + 1 = 0$, for $u + iv$ where $u, v \in \mathbb{F}_p$.
The $p^2$ elements in $\mathbb{F}_{p^2}$ has a subset of size $\floor*{\frac{p}{12}} + z$, where $z\in\{0, 1, 2\}$, the value of z depends on $p$ $(mod$ $12)$. 
This is the set of supersingular $j$-invariants in $\mathbb{F}_{p^2}$. Here we will implement a toy example [2] with $p=431,$ and thus there are $\floor*{\frac{431}{12}}+2=37$ supersingular $j-$invariants in $\mathbb{F}_{p^2}$, as shown in Figure 2. The 37 $j$-invariants are all of the supersingular $j$-invariants in characteristic $p$, note that supersingular curves always have $j$-invariants in $\mathbb{F}_{p^2}$, and there are no more in higher extension fields.
\begin{figure}[h]
\centering
\includegraphics[scale=0.55] {Fig5.PNG}
	\caption{The set of $37$ supersingular $j$-invariants in $\mathbb{F}_{431^2}$.}
	\label{fig1}
\end{figure}\par
For every elliptic curve, there exist a unique $j$-invariants, and two elliptic curves are isomorphic if and only if they have the same $j$-invariants.
For example, the elliptic curves in Montgomery form [10]
\[E_a: y^2=x^3+ax^2+x,\] 
have the $j-invariant$
\[j(E_a): \frac{256(a^2-3)^3}{a^2-4}.\]
Let $a_1=208i+161$ and $a_2=172i+162$, then $j(E_1)=j(E_2)=364i+302$, and
\[E_{a_1}: y^2=x^3+(208i+161)x^2+x,\]
\[E_{a_2}: y^2=x^3+(172i+162)x^2+x.\]
Therefore $E_{a_1}$ and $E_{a_2}$ are isomorphic, and correspond to the leftmost $j-invariant$ shown in Figure 4. The isomorphisms are
\[\psi: E_{a_1} \rightarrow E_{a_2}=(x,y) \rightarrow ((66i+182)x,(300i+109),(122i+159)y)\]
\[\psi^{-1}: E_{a_2} \rightarrow E_{a_1}=(x,y) \rightarrow ((156i+40)x,(304i+202),(419i+270)y)\]

In general, an isogeny is a map
\[\phi: E \rightarrow E^{\prime}\]
from one elliptic curve to another. Isogenies are said to be either separable or inseparable, the key is that separable isogenies are in one-to-one correspondence with finite subgroups: every subgroup $G$ of points on an elliptic curve $E$ gives rise to a unique isogeny. 
\par
Suppose $G = \{\mathcal{O},(\alpha, 0),(\frac{1}{\alpha}, 0),(0, 0)\}$, where $\mathcal{O}$ is the identity element, and
\[\phi: E_a \rightarrow E_{a^{\prime}},\]
\[x \rightarrow \frac{x(x\alpha-1)}{(x-\alpha)},\]
\[a^{\prime}=2(1-2\alpha^2).\]
This map can be used to compute 2-isogenies on any Montgomery curve. 
For example, one of the curves we mentioned
\[E_a: y^2=x^3+(208i+161)x^2+x,\]
\[j(E_{a})={364i+304}.\]
The point ($\alpha$, 0) $\in E_a$ with $\alpha = 350i + 68$ has order 2, then
\[E_{a^{\prime}}: y^2=x^3+(102i+423)x^2+x\]
\[j(E_{a^\prime})={344i+190}.\]
and the map
\[\phi: x \rightarrow \frac{x((321i+56)x-1)^2}{(x-(321i+56))^2}\]
will take any point not in (${\mathcal{O},(\alpha, 0)}$) to the corresponding point on $E_{a_0}$.

The important notion is that when we introduce any other prime $l \neq p$ (i.e. p=431) this set becomes a graph with interestingly surprising properties.
The vertices of each graph remain fixed as the $j$-invariants themselves, but the edges between them correspond to $l$-isogenies.
In SIDH we only need two of the graphs: $l= 2$ for Alice and $l= 3$ for Bob. (two smallest primes for easier implementation).
\par
Recall that the previous 2-isogenies
$\{E_{a},j(E_{a})\} \rightarrow \{E_{a^{\prime}},j(E_{a^\prime})\},$
we can draw an edge between these two $j$-invariants. There are two other kernels of 2-isogenies on $E_a$: one generated by ($\frac{1}{\alpha}, 0$) and the other generated by ($0, 0$). These produce two more edges, one connecting $j=364i+304$ to $j=67$ and one connecting $j=364i+304$ to $j=319$.
We can visit every $j$-invariant in the graph until
all three 2-torsion points have been used, depicted in Figure 3.
\begin{figure}[H]
\centering
\includegraphics[scale=0.425] {Fig6.PNG}
	\caption{The 2-isogeny graph for $p = 431$, the edges correspond to 2-isogenies.}
\end{figure}\par
Observe that, for all $j \notin \{0, 4, 242\}$, there are exactly 3 edges connecting a given node to other 2-isogenous $j$-invariants. 
\par
The 3-isogeny graph is drawn analogously, but there are now four outgoing edges corresponding to every $j$-invariant (exceptions for $j \in \{0, 4, 125, 242\}$). The point ($β\beta, \gamma$) = ($321i + 56, 303i + 174$) of order 3 on $E_a$ is the kernel of an isogeny to the curve 
\[E_{a^{\prime}}: y^2=x^3+415x^2+x,\]
\[j(E_{a^\prime})={189}.\]
Inputting the three other 3-torsion subgroups gives three image curves with $j = 19$, $j = 42i + 141$, and $j = 106i + 379$. 
Again, working through each node gives Figure 4.

\begin{figure}[H]
\centering
\includegraphics[scale=0.5] {Fig7.PNG}
	\caption{The 3-isogeny graph for $p = 431$. The 37 nodes are the supersingular $j$-invariants and the edges between them correspond to 3-isogenies.}
\end{figure}\par

The SIDH typically take prime in the form
\[p = 2^{e_A}3^{e_B}-1,\]
with
\[2e^A \approx 3e^B.\]
Alice’s secrets will be isogenies of degree $2^{e_A}$, and Bob’s will be of degree $3^{e_B}$. Alice will compute generators of her secret subgroups by computing secret linear combinations of two public basis points, where
\[ {\langle P_A,Q_A \rangle}=E[2^{e_A}]\cong{\mathbb{Z}_{2^{e_A}}}\times{\mathbb{Z}_{2^{e_A}}}. \]
Similarly, Bob computes
\[ {\langle P_B,Q_B \rangle}=E[3^{e_B}]\cong{\mathbb{Z}_{3^{e_B}}}\times{\mathbb{Z}_{3^{e_B}}}. \]
This necessarily means $P_A$ and $Q_A$ both have order $2^{e_A}$ and PB and QB both have order $3^{e_B}$. In practice (e.g. in SIKE [8]), they will typically choose their secret generator points, SA and SB, by simply taking
\[ S_A=P_A+[k_A]Q_A, \;\; [k_A]\in[0,2^{e_A}), \]
and
\[ S_B=P_B+[k_B]Q_B, \;\; [k_b]\in[0,3^{e_b}). \]
\par
We are not going through the full details of the state of the art SIDH protocol, but the key idea is that these isogenies can be used to generate the public and private keys (Figure 5, Figure 6, Figure 7 and Figure 8). The lack of any meaningful connection between Alice and Bob’s graphs is the heart of SIDH security.
\end{section}


\begin{figure}[H]
\centering
\includegraphics[scale=0.545] {Fig8.PNG}
	\caption{Alice’s key generation starts at the public curve corresponding to $j =
87i + 190$, her secret key is the isogeny $\phi_A = (\phi_3\cdot\phi_2\cdot\phi_1\cdot\phi_0)$, and the destination
node $222i + 118$ becomes part of her public key.}
\end{figure}

\begin{figure}[H]
\centering
\includegraphics[scale=0.5] {Fig9.PNG}
	\caption{Bob’s key starts at the public curve corresponding to $j=87i+190$, his secret key is the isogeny $\phi_B= (\phi_2\cdot\phi_1\cdot\phi_0)$, and the destination node $344i+190$ becomes part of his public key.}
\end{figure}

\begin{figure}[H]
\centering
\includegraphics[scale=0.5] {Fig10.PNG}
	\caption{Alice’s secret key starts at the curve from Bob’s public key with $j = 344i + 190$, and uses her secret key to compute the walk to the node with $j = 234$.}
\end{figure}

\begin{figure}[h]
\centering
\includegraphics[scale=0.5] {Fig11.PNG}
	\caption{Bob’s secret key starts at the curve from Alice’s public key with $j = 222i + 118$, and uses his secret key to compute the walk to the node with $j = 234$.}
\end{figure}


\begin{section}{Conclusion}
\hspace{5mm}The field of post-quantum cryptography is an ongoing research subject and of great ineterest for scientists. Other possible directions to construct post-quantum level cryptographies include Multivariate, Code-Based, and Hash-Based Cryptographies, where no known classical/quantum algorithm can easily solve. However, none of the above was accepted by the National Institute of Standards and Technology (NIST) [1], 
Although, currently, even the largest quantum computer is incapable of breaking any real cryptographic algorithm,  we should, however, prepare for the future when quantum computer does become a threat.
