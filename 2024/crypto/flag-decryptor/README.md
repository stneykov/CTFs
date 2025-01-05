# Flag decryptor (crypto) (author: stn)

## Description

```shell
This one is easy. Just run the program and it will print out the flag for you… eventually.
```
[Program.cs](Program.cs)

## Task analysis & solution

For this challenge, we've been provided with a Program.cs file that contains some C# code. Let's take a look and break it down:

```csharp
using System.Numerics;

namespace FlagDecryptor
{
    internal class Program
    {
        private const string Encrypted =
            "MTA5LDEwNCwxMDcs.....";

        private static bool x(BigInteger num)
        {
            if (num < 2)
            {
                return false;
            }

            for (int i = 2; i < num; i++)
            {
                if (num % i == 0)
                {
                    return false;
                }
            }
            return true;
        }

        private static BigInteger z(int num)
        {
            return BigInteger.Pow(2, num) - 1;
        }

        static void Main(string[] args)
        {
            var parsed = System.Text.Encoding.Default.GetString(Convert.FromBase64String(Encrypted));
            
            var cipher = parsed.Split(',').Select(BigInteger.Parse).ToArray();

            Console.Write("MilestoneCTF{");
            int count = 0;
            int i = 0;
            while (count < parsed.Length)
            {
                if (x(z(i)))
                {
                    Console.Write((char)(cipher[count] ^ z(i)));
                    count++;
                }
                i++;
            }

            Console.WriteLine("}");
        }
    }
}
```

The variable Encrypted contains a Base64-encoded string of comma-separated integers:

```csharp
private const string Encrypted = "MTA5LDEwNCwxMDcs.....";
```
This string represents a Base64-encoded set of integers, which are comma separated (I have intentionally ommitted it for brevity). 

The function ``z(int num)`` computes Mersenne numbers. A Mersenne number is of the form 2^𝑛−1, where 𝑛 is a positive integer. 
The function ``x(BigInteger num)`` determines whether a given number is prime and together, these two functions check whether **2^𝑛 - 1** is a **Marsenne prime**.

The decryption logic is fairly simple, once you have determined that a particular number is a Marsenne prime:

```csharp
while (count < parsed.Length)
{
    if (x(z(i)))
    {
        Console.Write((char)(cipher[count] ^ z(i)));
        count++;
    }
    i++;
}
```

- The ``cipher`` array is XORed with successive Mersenne primes to produce the ASCII values of the decrypted flag characters.
- The ``count`` variable ensures that only one encrypted value is decrypted per valid Mersenne prime.

There is an important remark that needs to be made here. **Even though the loop goes from ``count`` to ``parsed.Length`` (which is a very big number!), each of the 32 integers will be decrypted by a subsequent particular Marsenne prime.**

Now, the choice of Mersenne primes ensures the challenge is computationally interesting since generating primes requires validation and grows exponentially. This makes printing the flag by just running the program extremely time inefficient, yet not impossible. There are some optimizations that could be done. For example, the current function for checking primes is extremely slow. Using advanced algorithms like the Sieve of Eratosthenes or the Miller-Rabin one would greatly improve performance. Using parallel processing to compute primes would also help a bunch. 

I opted out of trying to optimize the current code. My solution was simple - the Marsenne primes, especially the first 32 of them are widely known and a search away:
- https://www.mersenne.org/primes/
- https://en.wikipedia.org/wiki/Mersenne_prime

Thus if I take just the exponents for the first 32, I could easily precalculate them:

```csharp
private static BigInteger[] GenerateMersenne()
{
    int[] primeExponents = { 2, 3, 5, 7, 13, 17, 19, 31, 61, 89, 107, 127, 521, 607, 1279, 2203, 2281, 3217, 4253, 4423, 9689,
    9941, 11213, 19937, 21701, 23209, 44497, 86243, 110503, 132049, 216091, 756839 };

    BigInteger[] mersennePrimes = new BigInteger[primeExponents.Length];

    for (int i = 0; i < primeExponents.Length; i++)
    {
        int p = primeExponents[i];
        mersennePrimes[i] = (BigInteger.One << p) - 1;
    }

    return mersennePrimes;
}
```

And then my updated program would look like this:

```csharp
using System.Numerics;

namespace FlagDecryptor
{
    internal class Program
    {
        private const string Encrypted =
            "MTA5LDEwNCwxMDcs.....";

        static void Main(string[] args)
        {
            var stopwatch = new Stopwatch();
            stopwatch.Start();

            var parsed = Encoding.Default.GetString(Convert.FromBase64String(Encrypted));
            var cipher = parsed.Split(',').Select(BigInteger.Parse).ToArray();
            var mersennePrimes = GenerateMersenne();

            Console.Write("MilestoneCTF{");

            for (int j = 0; j < cipher.Length; j++)
            {
                Console.Write((char)(cipher[j] ^ mersennePrimes[j]));
            }

            Console.Write("}");

            Console.WriteLine();
            Console.WriteLine($"Flag printed in {stopwatch.Elapsed}");
            Console.ReadKey();
        }

        private static BigInteger[] GenerateMersenne()
        {
            int[] primeExponents = { 2, 3, 5, 7, 13, 17, 19, 31, 61, 89, 107, 127, 521, 607, 1279, 2203,    2281,  3217, 4253, 4423, 9689,
            9941, 11213, 19937, 21701, 23209, 44497, 86243, 110503, 132049, 216091, 756839 };

            BigInteger[] mersennePrimes = new BigInteger[primeExponents.Length];

            for (int i = 0; i < primeExponents.Length; i++)
            {
                int p = primeExponents[i];
                mersennePrimes[i] = (BigInteger.One << p) - 1;
            }

            return mersennePrimes;
        }
    }
}
```

And this is the output from my updated code, which runs for about 200ms:

```shell
MilestoneCTF{not-the-primes-you-expected-42d4}
Flag printed in 00:00:00.1646550
```

The flag was **``MilestoneCTF{not-the-primes-you-expected-42d4}``**