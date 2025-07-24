# HTB - Pinned Walkthrough

Walkthrough of the Pinned mobile application challenge on Hack The Box.

## Tools Used

- jadx-gui
- openjdk 21.0.7 2025-04-15

## Analysis

Opening up the apk in jadx, we find in the MainActivity a function `x` (or something else if you
used deobfuscation) that seems to construct an HTTP request to POST using url encoded parameters.
This seems to specify the password using a cipher to decrypt the password for the request.

The app seems to have obfuscated the strings by nesting individual characters in a series of
subclasses and functions that reference characters from a list of strings given the index of the
string and the character index within that string.

### Ciphertext

The ciphertext seems to be a base64 encoded string that is constructed using various list operations
to build out the base64 value:

```java
urlparams.append(new String(cipher.doFinal(Base64.decode(ciphertextBase64, 0)), "utf-8"));
```

### Solution

To make this easy, we can export the project with jadx-gui to a new folder somewhere. Then we simply can copy the code
out that generates the string into a new java file:

```java
// GetTheFlag.java
import java.util.Base64;
import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import javax.crypto.Cipher;
import java.security.NoSuchAlgorithmException;
import java.security.InvalidKeyException;
import javax.crypto.NoSuchPaddingException;
import javax.crypto.IllegalBlockSizeException;
import javax.crypto.spec.SecretKeySpec;
import javax.crypto.BadPaddingException;
import java.io.UnsupportedEncodingException;
import p001b.p064q.C0554h;
import p069c.p073b.p074a.C0589a;
import p069c.p073b.p074a.C0590b;
import p069c.p073b.p074a.C0591c;
import p069c.p073b.p074a.C0592d;
import p069c.p073b.p074a.C0593e;
import p069c.p073b.p074a.C0594f;
import p069c.p073b.p074a.C0595g;
import p069c.p073b.p074a.C0596h;
import p069c.p073b.p074a.C0597i;



public class DumpTheGoodStuff {
    public static String m2462x() throws UnsupportedEncodingException, BadPaddingException, NoSuchAlgorithmException, NoSuchPaddingException, IllegalBlockSizeException, InvalidKeyException {
        String str;
        StringBuilder sb = new StringBuilder();
        sb.append(C0592d.m2165a());
        sb.append(C0590b.m2163a());
        sb.append(C0596h.m2169a());
        sb.append(C0591c.m2164a());
        sb.append(C0597i.m2170a());
        ArrayList<String> arrayList = new ArrayList<String>();
        arrayList.add("9GDFt6");
        arrayList.add("83h736");
        arrayList.add("kdiJ78");
        arrayList.add("vcbGT6");
        arrayList.add("LPGt63");
        arrayList.add("kFgde4");
        arrayList.add("5drDr4");
        arrayList.add("Y6ttr5");
        arrayList.add("444w45");
        arrayList.add("hjKd56");
        sb.append((String) arrayList.get(4));
        sb.append(C0593e.m2166a());
        sb.append(C0594f.m2167a());
        ArrayList<String> arrayList2 = new ArrayList();
        arrayList2.add("TG7ygj");
        arrayList2.add("U8uu8i");
        arrayList2.add("gGtT56");
        arrayList2.add("84hYDG");
        arrayList2.add("yRCYDm");
        arrayList2.add("7ytr4E");
        arrayList2.add("j5jU87");
        arrayList2.add("yRCYDm");
        arrayList2.add("jd9Idu");
        arrayList2.add("kd546G");
        sb.append((String) arrayList2.get(7));
        sb.append(C0589a.m2162a());
        String ciphertextBase64 = sb.toString();
        SecretKeySpec secretKeySpec = new SecretKeySpec((String.valueOf(C0554h.m2071b().charAt(3)) + String.valueOf(C0554h.m2071b().charAt(0)) + String.valueOf(C0591c.m2164a().charAt(0)) + String.valueOf(C0589a.m2162a().charAt(8)).toUpperCase() + String.valueOf(C0596h.m2169a().charAt(1)) + String.valueOf(C0554h.m2071b().charAt(0)) + String.valueOf(C0597i.m2170a().charAt(5)).toUpperCase() + String.valueOf(C0589a.m2162a().charAt(7)) + String.valueOf(C0590b.m2163a().charAt(4)) + String.valueOf(C0593e.m2166a().charAt(4)) + String.valueOf(C0593e.m2166a().charAt(4)) + String.valueOf(C0597i.m2170a().charAt(5)).toUpperCase() + String.valueOf(C0592d.m2165a().charAt(3)) + String.valueOf(C0592d.m2165a().charAt(5)) + String.valueOf(C0596h.m2169a().charAt(1)) + String.valueOf(C0596h.m2169a().charAt(1))).getBytes(), C0595g.m2168a());
        Cipher cipher = Cipher.getInstance(C0595g.m2168a());
        cipher.init(2, secretKeySpec);
        return new String(cipher.doFinal(Base64.getDecoder().decode(ciphertextBase64)), "utf-8");
    }

    public static void main(String[] args) {
        try{
            System.out.println(m2462x());
        }catch (UnsupportedEncodingException | BadPaddingException | NoSuchAlgorithmException | NoSuchPaddingException | IllegalBlockSizeException | InvalidKeyException unused){

        }
    }
}
```

Then remove all the java files that are not used in this code. We can then compile the resultant structure.

```bash
$ javac ./GetTheFlag.java \
  ./p069c/p073b/p074a/C0594f.java \
  ./p069c/p073b/p074a/C0590b.java \
  ./p069c/p073b/p074a/C0596h.java \
  ./p069c/p073b/p074a/C0592d.java \
  ./p069c/p073b/p074a/C0589a.java \
  ./p069c/p073b/p074a/C0591c.java \
  ./p069c/p073b/p074a/C0595g.java \
  ./p069c/p073b/p074a/C0597i.java \
  ./p069c/p073b/p074a/C0593e.java \
  ./p001b/p064q/C0554h.java
```

Finally, we can run the program to get the flag:

```bash
$ java GetTheFlag
HTB{this_is_not_the_real_flag}
```
