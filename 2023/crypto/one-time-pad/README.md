# One time pad (crypto) (author: stn)

## Tools

**nmap** – a network scanner, useful for scanning certain hosts for open ports and figuring out what is listening on those
**telnet client tool** – command-line tool used for initiating a connection to the target machine, could be installed as a Windows feature (PuTTy is also an option here, or any other tool that can perform the same)

*some IDE and a bit of programming knowledge might go a long way with the last part of the solution, although there probably is a tool online that could help with the same.


## Description

```
Connect to onetimepad.cyberxmas2023.psirt.maketheworldsee.com 

Can you retrieve the flag?
```

## Task analysis & solution

In the task, we get an address (onetimepad.cyberxmas2023.psirt.maketheworldsee.com) and a DLL file, called OneTimePad.dll.

By running the dll through an application like dotPeek, we can see that it is a .net application written in .net core. Let’s examine the code it decompiles to:

```csharp

internal class Program
  {
    private static readonly int KeySize = 50000;
    private static int keyIndex = 0;

    private static void Main(string[] args)
    {
      Console.WriteLine("Welcome!");
      Console.WriteLine("If you are looking for the flag, here you go: ");
      Console.WriteLine(Program.Encrypt(File.ReadAllText("flag")));
      Console.WriteLine("Oh... I am sorry, the flag is encrypted! :(");
      while (true)
      {
        Console.WriteLine("What else should I encrypt?");
        string input = Console.ReadLine();
        if (!string.IsNullOrEmpty(input))
        {
          if (input.Length <= Program.KeySize)
          {
            Console.WriteLine("No problem!");
            Console.WriteLine(Program.Encrypt(input));
          }
          else
            goto label_4;
        }
        else
          break;
      }
      Console.WriteLine("Have a great day!");
      return;
label_4:
      Console.WriteLine("That message is too long!");
    }

    private static string Encrypt(string input)
    {
      byte[] key = Program.GetKey(input.Length);
      byte[] bytes = Encoding.UTF8.GetBytes(input);
      string empty = string.Empty;
      for (int index = 0; index < bytes.Length; ++index)
        empty += ((int) bytes[index] ^ (int) key[index]).ToString("x2");
      return empty;
    }

    private static byte[] GetKey(int length)
    {
      byte[] source1 = File.ReadAllBytes("key");
      IEnumerable<byte> source2;
      if (Program.keyIndex + length > Program.KeySize)
      {
        int count = Program.KeySize - Program.keyIndex;
        IEnumerable<byte> first = ((IEnumerable<byte>) source1).Skip<byte>(Program.keyIndex).Take<byte>(count);
        length -= count;
        source2 = first.Concat<byte>(((IEnumerable<byte>) source1).Take<byte>(length));
        Program.keyIndex = length;
      }
      else
      {
        source2 = ((IEnumerable<byte>) source1).Skip<byte>(Program.keyIndex).Take<byte>(length);
        Program.keyIndex += length;
      }
      return source2.ToArray<byte>();
    }
  }


```

At first glance, the application seems to implement a simple one-time pad encryption. We read the key from a file called ‘key’ and the flag from a file called ‘flag’. The key is then used to en-crypt input in a loop, after first encrypting the flag and printing it to the console. In the GetKey() method, one can see that we actually don’t take the whole key every time, but we actually start from an index that is increased by the length of the input we want to encrypt every time and we work with a subset of that key. When the index plus the length of the input exceeds or is equal to the size of the key, then we wrap around to the beginning of the key file. It is very important to note that the flag is always encrypted with the first ‘flag length’ number of characters.

Once we get the key subset used to encrypt, we XOR each character of the input with the character at the same index within the key subset. Then we convert the result to a hexadecimal string. Also, important to keep in mind, XOR could be reversed with an XOR 😊

But enough with the program, next, let’s examine the address that was provided. Naturally, one would try to put it in a browser or ping it, but it doesn’t seem to respond to pings or HTTP requests (at least not on the default port 80). Let’s scan for open ports with nmap:

```shell

└─$ sudo nmap onetimepad.cyberxmas2023.psirt.maketheworldsee.com -Pn                                                                                                                                                                       
Starting Nmap 7.94SVN ( https://nmap.org ) at 2023-12-03 10:11 HST
Nmap scan report for onetimepad.cyberxmas2023.psirt.maketheworldsee.com (172.160.242.85)
Host is up (0.046s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT     STATE SERVICE
22/tcp   open  ssh
9999/tcp open  abyss

Nmap done: 1 IP address (1 host up) scanned in 5.85 seconds

```

We can see two open ports – 22 and 9999, both on TCP. 22 is the default SSH port, but that 9999 port looks suspicious, let’s hit it with telnet 😊

```shell

C:\Users\stn>telnet onetimepad.cyberxmas2023.psirt.maketheworldsee.com 9999
Trying 172.160.242.85...
Connected to onetimepad.cyberxmas2023.psirt.maketheworldsee.com.
Escape character is '^]'.
Welcome!
If you are looking for the flag, here you go:
77f9bee29912e1ea70bcba9e5876cd1f1c90ae32161ca62197e6e998985321859b743c613066f8d07c0346
Oh... I am sorry, the flag is encrypted! :(
What else should I encrypt?

```

From the analysis we have done so far, we can conclude that the program we’ve been provided with runs on this address, on port 9999. We quickly realize that regardless of how many telnet sessions we open, the encrypted flag is always the same - **77f9bee29912e1ea70bcba9e5876cd1f1c90ae32161ca62197e6e998985321859b743c613066f8d07c0346** and we can safely say the key does not change. We already know how the ‘encryption’ is performed and we know XOR can be reversed. 

The encrypted flag has 86 symbols, and we already noted that the output is transformed into a two-digit hexadecimal in code, thus we can say that the flag itself has 43 characters. In theory, and considering we know that we can wrap around the key file, we should be able to do that with some carefully crafted input and then feed the program 43 symbols as input – the size of the flag. Doing that, we should be able to get the encrypted value for that input and just reverse (XOR) that subset of the key and then subsequently use it to reverse our flag. Let’s try it. 

From the code we decompiled, we know that the size of the key is 50000. The program automatically feeds 43 characters into it when it encrypts the flag, so we need to input another 49957 (any input will do, just make sure you have the number of characters exactly at that number!). Easy. Let’s input our 43-symbol string now, say **ddddddddddddddddddddddddddddddddddddddddddd**.

```
What else should I encrypt?
ddddddddddddddddddddddddddddddddddddddddddd
No problem!
5ef4b6e38e02eae0719b8abc477ddd0b5586af270711b02080afecd18f5e2b86937575702767b1df7d1e5f
What else should I encrypt?
```

**5ef4b6e38e02eae0719b8abc477ddd0b5586af270711b02080afecd18f5e2b86937575702767b1df7d1e5f** is the result of our input that should be encrypted with the exact same subset of the key used to encrypt the flag. Let’s now craft a simple program to ‘decrypt’ 😊

```csharp
internal class Program
{
    static void Main()
    {

        string input = "ddddddddddddddddddddddddddddddddddddddddddd";
        byte[] inputBytes = Encoding.UTF8.GetBytes(input);

        string encryptedInput = "5ef4b6e38e02eae0719b8abc477ddd0b5586af270711b02080afecd18f5e2b86937575702767b1df7d1e5f";
        byte[] encryptedInputBytes = HexStringToByteArray(encryptedInput);

        var empty = new List<byte>();

        for (int index = 0; index < encryptedInputBytes.Length; ++index)
            empty.Add((byte)(encryptedInputBytes[index] ^ inputBytes[index]));

        var keyBytes = empty.ToArray();
        empty.Clear();

        string flag = "77f9bee29912e1ea70bcba9e5876cd1f1c90ae32161ca62197e6e998985321859b743c613066f8d07c0346";
        var flagBytes = HexStringToByteArray(flag);

        for (int index = 0; index < flagBytes.Length; ++index)
            empty.Add((byte)((int)flagBytes[index] ^ (int)keyBytes[index]));

        var finalResult = Encoding.UTF8.GetString(empty.ToArray());
        Console.WriteLine(finalResult);
    }

    static byte[] HexStringToByteArray(string hex)
    {
        hex = hex.Replace(" ", "").Replace("-", "");
        if (hex.Length % 2 != 0)
        {
            hex = "0" + hex;
        }

        byte[] byteArray = new byte[hex.Length / 2];

        for (int i = 0; i < byteArray.Length; i++)
        {
            byteArray[i] = Convert.ToByte(hex.Substring(i * 2, 2), 16);
        }

        return byteArray;
    }
}

```

We ‘decrypt’ the subset of the key using the encrypted value that we got for our crafted input. Then we use the key’s byte array to decrypt the encrypted flag we get every time we connect to onetimepad.cyberxmas2023.psirt.maketheworldsee.com.

Enjoy & happy hacking! 😊 The flag is: **MilestoneCTF{otp-requires-a-single-use-key}**
