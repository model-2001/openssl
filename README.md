RSA:
import java.util.*;

class RSA {

    // GCD
    static long gcd(long a, long b) {
        while (b != 0) {
            long t = b;
            b = a % b;
            a = t;
        }
        return a;
    }

    // Simple modular power
    static long power(long base, long exp, long n) {
        long result = 1;

        while (exp-- > 0) {
            result = (result * base) % n;
        }

        return result;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        // Activity 1
        System.out.print("Enter prime p: ");
        long p = sc.nextLong();

        System.out.print("Enter prime q: ");
        long q = sc.nextLong();

        // Activity 2
        long n = p * q;
        long phi = (p - 1) * (q - 1);

        long e = 2;

        while (gcd(e, phi) != 1) {
            e++;
        }

        long d = 1;

        while ((e * d) % phi != 1) {
            d++;
        }

        System.out.println("\nPublic Key  = (" + e + ", " + n + ")");
        System.out.println("Private Key = (" + d + ", " + n + ")");

        // Activity 3
        sc.nextLine();

        System.out.print("\nEnter plaintext: ");
        String text = sc.nextLine();

        long[] cipher = new long[text.length()];

        System.out.print("Encrypted: ");

        for (int i = 0; i < text.length(); i++) {

            long m = text.charAt(i);

            cipher[i] = power(m, e, n);

            System.out.print(cipher[i] + " ");
        }

        // Activity 4
        System.out.print("\nDecrypted: ");

        for (long c : cipher) {

            long m = power(c, d, n);

            System.out.print((char) m);
        }
    }
}

------------------------------------------------------
integer factorization


import java.util.*;

class RSA {

    // GCD
    static long gcd(long a, long b) {
        while (b != 0) {
            long t = b;
            b = a % b;
            a = t;
        }
        return a;
    }

    // Simple modular power
    static long power(long base, long exp, long n) {
        long result = 1;

        while (exp-- > 0) {
            result = (result * base) % n;
        }

        return result;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        // Given Public Key
        System.out.print("Enter public exponent e: ");
        long e = sc.nextLong();

        System.out.print("Enter n: ");
        long n = sc.nextLong();

        // Integer Factorization
        long p = 0, q = 0;

        for (long i = 2; i * i <= n; i++) {
            if (n % i == 0) {
                p = i;
                q = n / i;
                break;
            }
        }

        if (p == 0 || q == 0 || p == q) {
            System.out.println("Invalid RSA key");
            sc.close();
            return;
        }

        // Calculate phi
        long phi = (p - 1) * (q - 1);

        // Find Private Key
        if (gcd(e, phi) != 1) {
            System.out.println("Invalid public exponent");
            sc.close();
            return;
        }

        long d = 1;

        while ((e * d) % phi != 1) {
            d++;
        }

        System.out.println("\np = " + p);
        System.out.println("q = " + q);

        System.out.println("Public Key  = (" + e + ", " + n + ")");
        System.out.println("Private Key = (" + d + ", " + n + ")");

        // Encryption
        System.out.print("\nEnter plaintext number: ");
        long m = sc.nextLong();

        if (m < 0 || m >= n) {
            System.out.println("Plaintext must be between 0 and n-1");
            sc.close();
            return;
        }

        long c = power(m, e, n);

        System.out.println("Encrypted: " + c);

        // Decryption
        long decrypted = power(c, d, n);

        System.out.println("Decrypted: " + decrypted);

        sc.close();
    }
}

---------------------------------------------------------------------

DSS:
import java.util.*;

class DSS {

    // Calculate (base^exp) mod mod
    static long modPow(long base, long exp, long mod) {
        long result = 1;

        while (exp > 0) {
            result = (result * base) % mod;
            exp--;
        }

        return result;
    }

    // Find modular inverse
    static long modInverse(long a, long mod) {

        for (long i = 1; i < mod; i++) {
            if ((a * i) % mod == 1) {
                return i;
            }
        }

        return -1;
    }

    // Generate g and y
    static long[] generateKeys(long p, long q, long h, long x) {

        long g = modPow(h, (p - 1) / q, p);

        long y = modPow(g, x, p);

        return new long[]{g, y};
    }

    // Generate signature
    static long[] generateSignature( long hm, long k, long x,long p, long q, long g) {

        long r = modPow(g, k, p) % q;

        long kInverse = modInverse(k, q);

        long s = (kInverse * (hm + x * r)) % q;

        return new long[]{r, s};
    }

    // Verify signature
    static boolean verifySignature( long r, long s, long hm,long p, long q, long g, long y) {

        long w = modInverse(s, q);

        long u1 = (hm * w) % q;

        long u2 = (r * w) % q;

        long v = ((modPow(g, u1, p)  * modPow(y, u2, p)) % p) % q;

        return v == r;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        System.out.print("Enter p: ");
        long p = sc.nextLong();

        System.out.print("Enter q: ");
        long q = sc.nextLong();

        System.out.print("Enter h: ");
        long h = sc.nextLong();

        System.out.print("Enter private key x: ");
        long x = sc.nextLong();

        // Key Generation
        long[] key = generateKeys(p, q, h, x);

        long g = key[0];
        long y = key[1];

        System.out.println("\nGenerator g = " + g);
        System.out.println("Private Key x = " + x);
        System.out.println("Public Key y = " + y);

        // Message hash
        System.out.print("\nEnter message hash h(m): ");
        long hm = sc.nextLong();

        System.out.print("Enter secret k: ");
        long k = sc.nextLong();

        // Signature
        long[] signature =
                generateSignature(hm, k, x, p, q, g);

        long r = signature[0];
        long s = signature[1];

        System.out.println("\nSignature:");
        System.out.println("r = " + r);
        System.out.println("s = " + s);

        // Verification
        boolean valid =
                verifySignature(r, s, hm, p, q, g, y);

        System.out.println("\nSignature valid = " + valid);
    }
}
----------------------------------------------------------------------

hill enc and known plain:

import java.util.*;

class HillCipher {
    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        System.out.print("Enter plaintext: ");
        String text = sc.nextLine().toUpperCase();

        int[][] key = new int[2][2];

        System.out.println("Enter 2x2 key matrix:");

        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                key[i][j] = sc.nextInt();
            }
        }

        // Add X if length is odd
        if (text.length() % 2 != 0) {
            text = text + "X";
        }

        String result = "";

        for (int i = 0; i < text.length(); i += 2) {

            int p1 = text.charAt(i) - 'A';
            int p2 = text.charAt(i + 1) - 'A';

            int c1 = (key[0][0] * p1 +
                      key[0][1] * p2) % 26;

            int c2 = (key[1][0] * p1 +
                      key[1][1] * p2) % 26;

            result += (char)(c1 + 'A');
            result += (char)(c2 + 'A');
        }

        System.out.println("Cipher Text: " + result);
    }
}

----------------------------------------------------------------------------------------------------------------------------------------------
knowwn plain-txt

import java.util.*;

class KnownPlaintextAttack {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter known plaintext: ");
        String plain = sc.nextLine().toUpperCase();

        System.out.print("Enter corresponding ciphertext: ");
        String cipher = sc.nextLine().toUpperCase();

        boolean found = false;

        for (int a = 0; a < 26; a++) {
            for (int b = 0; b < 26; b++) {
                for (int c = 0; c < 26; c++) {
                    for (int d = 0; d < 26; d++) {

                        String result = "";

                        for (int i = 0; i < plain.length(); i += 2) {
                            int p1 = plain.charAt(i) - 'A';
                            int p2 = plain.charAt(i + 1) - 'A';

                            int c1 = (a * p1 + b * p2) % 26;
                            int c2 = (c * p1 + d * p2) % 26;

                            result += (char)(c1 + 'A');
                            result += (char)(c2 + 'A');
                        }

                        if (result.equals(cipher)) {
                            System.out.println("Key Matrix:");
                            System.out.println(a + " " + b);
                            System.out.println(c + " " + d);
                            found = true;
                            break;
                        }
                    }
                    if (found) break;
                }
                if (found) break;
            }
            if (found) break;
        }

        if (!found) {
            System.out.println("Key not found");
        }

        sc.close();
    }
}
--------------------------------------------------------------------------------------
 hill dec:
 import java.util.*;

class HillDecrypt {
    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        System.out.print("Enter ciphertext: ");
        String text = sc.nextLine().toUpperCase();

        int[][] key = new int[2][2];

        System.out.println("Enter 2x2 key matrix:");

        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                key[i][j] = sc.nextInt();
            }
        }

        // Find determinant
        int det = key[0][0] * key[1][1]
                - key[0][1] * key[1][0];

        det = (det % 26 + 26) % 26;

        // Find determinant inverse
        int detInverse = 0;

        for (int i = 1; i < 26; i++) {
            if ((det * i) % 26 == 1) {
                detInverse = i;
                break;
            }
        }

        // Inverse key matrix
        int a = key[0][0];
        int b = key[0][1];
        int c = key[1][0];
        int d = key[1][1];

        int[][] inv = new int[2][2];

        inv[0][0] = (d * detInverse) % 26;
        inv[0][1] = ((-b + 26) * detInverse) % 26;
        inv[1][0] = ((-c + 26) * detInverse) % 26;
        inv[1][1] = (a * detInverse) % 26;

        String result = "";

        for (int i = 0; i < text.length(); i += 2) {

            int c1 = text.charAt(i) - 'A';
            int c2 = text.charAt(i + 1) - 'A';

            int p1 = (inv[0][0] * c1 +
                      inv[0][1] * c2) % 26;

            int p2 = (inv[1][0] * c1 +
                      inv[1][1] * c2) % 26;

            result += (char)(p1 + 'A');
            result += (char)(p2 + 'A');
        }

        System.out.println("Plain Text: " + result);
    }
}
------------------------------------
Diffie:

import java.math.BigInteger;
import java.util.*;

public class DH {

    // Function to generate a primitive root
    static int primitiveRoot(int p) {
        for (int g = 2; g < p; g++) {
            boolean valid = true;

            for (int i = 1; i < p - 1; i++) {
                if (power(g, i, p) == 1) {
                    valid = false;
                    break;
                }
            }

            if (valid)
                return g;
        }
        return -1;
    }

    // Function to calculate (base^exp) % p
    static int power(int base, int exp, int p) {
        int result = 1;
        for (int i = 0; i < exp; i++)
            result = (result * base) % p;
        return result;
    }

    // Function to generate public key
    static int publicKey(int g, int privateKey, int p) {
        return power(g, privateKey, p);
    }

    // Function to generate shared secret key
    static int sharedSecret(int publicKey, int privateKey, int p) {
        return power(publicKey, privateKey, p);
    }

    public static void main(String[] args) {
        int p = 23;

        int g = primitiveRoot(p);

        int a = 6;   // Client private key
        int b = 15;  // Server private key

        int A = publicKey(g, a, p);
        int B = publicKey(g, b, p);

        int key1 = sharedSecret(B, a, p);
        int key2 = sharedSecret(A, b, p);

        System.out.println("Prime number p = " + p);
        System.out.println("Primitive root g = " + g);
        System.out.println("Client public key = " + A);
        System.out.println("Server public key = " + B);
        System.out.println("Client shared secret = " + key1);
        System.out.println("Server shared secret = " + key2);

        if (key1 == key2)
            System.out.println("Shared secret keys match!");
        else
            System.out.println("Shared secret keys do not match!");
    }
}



-----------------------------------------------------------------------

import java.io.*;
import java.net.*;

public class DH {

    static int p = 23, g = 5;

    static int power(int base, int exp, int p) {
        int result = 1;
        for (int i = 0; i < exp; i++)
            result = (result * base) % p;
        return result;
    }

    static void server() throws Exception {
        ServerSocket ss = new ServerSocket(5000);
        System.out.println("Server waiting...");

        Socket s = ss.accept();

        DataInputStream in =
            new DataInputStream(s.getInputStream());
        DataOutputStream out =
            new DataOutputStream(s.getOutputStream());

        int b = 15; // Server private key
        int B = power(g, b, p);

        int A = in.readInt();
        out.writeInt(B);

        int key = power(A, b, p);

        System.out.println("Server public key = " + B);
        System.out.println("Server shared secret = " + key);

        s.close();
        ss.close();
    }

    static void client() throws Exception {
        Socket s = new Socket("localhost", 5000);

        DataInputStream in =
            new DataInputStream(s.getInputStream());
        DataOutputStream out =
            new DataOutputStream(s.getOutputStream());

        int a = 6; // Client private key
        int A = power(g, a, p);

        out.writeInt(A);

        int B = in.readInt();
        int key = power(B, a, p);

        System.out.println("Client public key = " + A);
        System.out.println("Client shared secret = " + key);

        s.close();
    }

    public static void main(String[] args) throws Exception {
        if (args.length == 0) {
            System.out.println("Use: java DH server");
            System.out.println("or:  java DH client");
        } else if (args[0].equalsIgnoreCase("server")) {
            server();
        } else if (args[0].equalsIgnoreCase("client")) {
            client();
        }
    }
}
has:

import hashlib
import itertools
import time

# Activity 1: Generate Passwords
chars = input("Enter characters: ")
length = int(input("Enter maximum password length: "))

passwords = []

start = time.time()

for n in range(1, length + 1):
    for p in itertools.product(chars, repeat=n):
        password = ''.join(p)
        passwords.append(password)

print("\nAll Passwords:")
for password in passwords:
    print(password)

print("Total passwords:", len(passwords))

# Activity 2: Build Hash Dictionary
table1 = {}

for password in passwords:
    h = hashlib.md5(password.encode()).hexdigest()
    table1[h] = password

print("\nHash Dictionary:")
for h, password in table1.items():
    print(h, ":", password)

# Activity 3: Create User Credential Table
table2 = {}
n = int(input("\nEnter number of users: "))

for i in range(n):
    user = input("Enter username: ")
    password = input("Enter password: ")

    h = hashlib.md5(password.encode()).hexdigest()
    table2[user] = h

# Activity 4: Dictionary Attack
print("\nRecovered Passwords:")

for user, h in table2.items():
    if h in table1:
        print(user, ":", table1[h])
    else:
        print(user, ": Password not found")

# Activity 6: Timing Analysis
end = time.time()

print("\nTime taken:", end - start, "seconds")

-----------------------------------------------------
ceaser:
import java.util.Scanner;

class CaesarBruteForce {

    static String decrypt(String text, int key) {

        String result = "";

        for (char c : text.toCharArray()) {

            if (Character.isLetter(c)) {

                if (Character.isUpperCase(c))
                    result += (char)((c - 'A' + 26 - key) % 26 + 'A');
                else
                    result += (char)((c - 'a' + 26 - key) % 26 + 'a');

            } else {
                result += c;
            }
        }

        return result;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        System.out.print("Enter ciphertext: ");
        String text = sc.nextLine();

        System.out.println("\nPossible plaintexts:");

        for (int key = 0; key < 26; key++) {
            System.out.println("Key " + key + ": " + decrypt(text, key));
        }
    }
}







-----------------------------------------------------------------------------------------------



import java.util.Scanner;

class CaesarBruteForce {

    static String encrypt(String text, int key) {
        String result = "";

        for (char c : text.toCharArray()) {
            if (Character.isLetter(c)) {
                if (Character.isUpperCase(c))
                    result += (char)((c - 'A' + key) % 26 + 'A');
                else
                    result += (char)((c - 'a' + key) % 26 + 'a');
            } else {
                result += c;
            }
        }
        return result;
    }

    static String decrypt(String text, int key) {
        return encrypt(text, 26-key);
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter ciphertext: ");
        String text = sc.nextLine();

        System.out.println("\nPossible plaintexts:");

        for (int key = 0; key < 26; key++) {
            System.out.println("Key " + key + ": " + decrypt(text, key));
        }

        sc.close();
    }
}



 --------------------------------------------------------------------------
 
import java.util.Scanner;

class FrequencyAnalysis {

    static String encrypt(String text, int key) {
        String result = "";

        for (char c : text.toCharArray()) {
            if (Character.isLetter(c)) {
                if (Character.isUpperCase(c))
                    result += (char)((c - 'A' + key) % 26 + 'A');
                else
                    result += (char)((c - 'a' + key) % 26 + 'a');
            } else {
                result += c;
            }
        }
        return result;
    }

    static String decrypt(String text, int key) {
        return encrypt(text, (26 - key) % 26);
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter plaintext: ");
        String plain = sc.nextLine();

        System.out.print("Enter key: ");
        int key = sc.nextInt();

        String cipher = encrypt(plain, key);
        System.out.println("Encrypted text: " + cipher);

        int[] freq = new int[26];

        for (char c : cipher.toUpperCase().toCharArray()) {
            if (c >= 'A' && c <= 'Z') {
                freq[c - 'A']++;
            }
        }

        int max = 0;
        char mostFrequent = 'A';

        System.out.println("\nLetter Frequencies:");

        for (int i = 0; i < 26; i++) {
            System.out.println((char)('A' + i) + " : " + freq[i]);

            if (freq[i] > max) {
                max = freq[i];
                mostFrequent = (char)('A' + i);
            }
        }

        int guessedKey = (mostFrequent - 'E' + 26) % 26;

        System.out.println("\nMost frequent letter: " + mostFrequent);
        System.out.println("Guessed key: " + guessedKey);
        System.out.println("Decrypted text: " + decrypt(cipher, guessedKey));

        sc.close();
    }
}
