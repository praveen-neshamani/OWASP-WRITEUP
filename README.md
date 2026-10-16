# CTF Competition Write-up: Our Journey, Challenges, and Flag Hunting

## Introduction: Let the Game Begin!

The CTF competition started, and our first thought was simple: let's find something easy and get started! We decided to explore different categories and see what we could break, decode, or discover.

What looked like a simple game of finding flags soon turned into an afternoon of debugging, decoding, investigating suspicious files, and occasionally questioning our own approach. Some challenges were solved quickly, while others gave us a proper headache. But every challenge taught us something new, and every successful flag recovery felt like a small victory.

Here is how our journey went, challenge by challenge!

## 1. Reverse Engineering: Code Maze

We first moved to the Reverse Engineering section and chose **Code Maze**, thinking it would be a good starting point.

The challenge description said that the Python script was heavily obfuscated. At first, we thought we had to understand the entire complicated-looking code. We spent some time overcomplicating things before finally spotting the real culprit: an obfuscated line involving the `exec()` function.

Instead of letting the code execute blindly, we changed our approach and modified the statement to print the content that was about to be executed. And there it was! We could finally see what was happening behind all that confusing code.

Sometimes, the solution is not to understand every complicated line, but simply to expose what the program is unpacking. We recovered the flag, submitted it, and moved on to the next challenge.

**Lesson learned:** Never let obfuscated code intimidate you. Sometimes, one small print statement is all it takes to turn a confusing program into a readable one!

## 2. Reverse Engineering: Binary File Challenge

Next, we moved to another reverse engineering challenge involving a binary file. The creator definitely knew how to make us work for it!

We tried investigating the file, exploring its structure, and looking for anything suspicious. Unfortunately, we could not recover the flag despite our attempts.

But hey, not every challenge ends with a flag, right?

During this investigation, we discovered around ten new tools and explored different techniques for file identification, metadata analysis, and steganography. We started understanding how different file formats could hide clues and why one tool might reveal something that another tool completely misses.

Although we could not crack this challenge, we walked away with new tools to explore and a better understanding of digital investigation.

**Lesson learned:** An unsolved challenge is not a wasted attempt. The real win was discovering practical techniques that will help us on the next problem!

## 3. Cryptography: Caesar's Secret

After reverse engineering, we moved to Cryptography. Our next target was **Caesar's Secret**.

The challenge description mentioned Julius Caesar and a shift somewhere between 1 and 25. So, the task was clear: find the correct shift and recover the flag.

We copied the encrypted message and used a cipher-decoding tool to test the possible shifts. Instead of trying to guess the key manually, we let the tool test all 25 offsets.

Once the correct shift was applied, the encrypted message turned into readable text, and we found our flag!

A simple classical cipher, a little logical thinking, and the right tool, job done!

**Lesson learned:** When the possible keys are limited, systematic testing is much faster than guessing. Caesar ciphers look mysterious at first, but with only 25 possibilities, brute force takes seconds.

## 4. Cryptography: Encoding Chaos

Next came **Encoding Chaos**, where the challenge description warned us that the message was not encoded using just one layer.

We copied the encoded content and started investigating what kind of transformation had been applied. To identify the encoding, we ran it through the **dCode Cipher Identifier**.

The results pointed towards repeated Base64 encoding. We followed the clue and decoded the content layer by layer, repeating the process until the message finally became readable.

And boom, we found the flag!

This challenge was a great reminder that unreadable text does not always mean complex encryption. Often, data is simply encoded multiple times, and the trick is recognizing the pattern.

**Lesson learned:** Don't immediately assume every strange-looking string requires advanced cryptography. Identify the encoding first, reverse the layers, and see what appears!

## 5. Cryptography: Weak Lock

Now came **Weak Lock**, another cryptography challenge, but this time the description gave us an important clue: the message was encrypted using a custom XOR cipher with a predictable four-byte key.

The task was to recover the secret message by figuring out the key pattern. We explored cryptographic tools and used dCode to inspect the ciphertext and test candidate keys.

During our attempts, we identified `0x41` as a promising hexadecimal key value. We used this clue to track down the rest of the XOR key and recover the secret text.

The interesting takeaway was that XOR itself was not broken; the predictable key pattern was the real weakness. Even simple encryption falls apart quickly when its key is predictable or poorly designed.

**Lesson learned:** The security of encryption depends heavily on the key. A predictable key can turn what looks like an impossible crypto puzzle into a quick solve.

## 6. Digital Forensics: Hidden in Plain Sight

Moving to Forensics, we selected **Hidden in Plain Sight**.

The challenge description mentioned intercepted network traffic that looked like random hexadecimal data. The question was simple: was there really a message hidden in all that random-looking content?

We examined the supplied `capture.txt` file and parsed through the hexadecimal data to see if it contained meaningful structure.

Instead of dismissing the content as noise, we searched for recognizable byte sequences and printable ASCII. That structure led us straight to the concealed payload inside the capture, allowing us to recover the flag.

Data that looks like random garbage at first glance can easily hide structured packets underneath once you filter for printable characters.

**Lesson learned:** In digital forensics, don't judge data by how it looks. Examine its structure, search for patterns, and let the headers guide you.

## 7. Digital Forensics: Pixel Secrets

Next was **Pixel Secrets**, where the challenge description mentioned a vacation photograph and hinted that something was hidden inside its pixels.

Seriously, who would suspect an ordinary vacation photo of hiding a flag?

We started by examining the image. The file was a 200 x 150 RGB PNG. We tried extracting strings and running Binwalk, but nothing interesting appeared.

Then we looked more closely at the pixel data.

We noticed something unusual in the blue colour channel: its value was 128 for most pixels, except for 52 pixels that broke the pattern. That was our clue!

The unusual distribution pointed us straight toward **Least Significant Bit (LSB) steganography**. We extracted the least significant bits from the pixels to assemble the raw bitstream.

After skipping the initial three null bytes, the flag appeared!

What looked like an ordinary image had been hiding information right inside its color channels. That was a great finding!

**Lesson learned:** When ordinary file carving reveals nothing, examining raw pixel values and color channels can uncover data hidden right under the surface.

## 8. Digital Forensics: Digital Footprints

Every photograph has details that don't meet the eye, and sometimes the best clues aren't in the image itself, but in the data attached to it.

For **Digital Footprints**, we were given a sunset beach photograph. The visible image contained a yellow circle and blue and green lines, but those elements did not reveal any text.

So, we checked the metadata.

And guess what? The metadata contained a sequence of numbers that looked like it was trying to tell us something!

We extracted the information and decoded it with Base64. However, the result only gave us flag fragments. We had to combine the recovered fragments with the challenge prompt to deduce the missing middle word.

Once we inserted that missing term between the pieces, the full flag came together.

A little metadata analysis, some decoding, and a bit of puzzle-solving, and we had our result!

**Lesson learned:** The visible image is only half the story. EXIF data and metadata headers can carry clues that are just as vital as the pixels.

## 9. Web Security: Hidden Profiles

After spending time with files and images, we moved to the Web category and selected **Hidden Profiles**.

The challenge description mentioned that user profiles were loaded using IDs and asked whether we could find the administrator's hidden profile. That was our clue!

We started navigating through user profiles and examining how the application handled the identifiers in the URL.

During our exploration, we tested user ID `0`. It turned out to be the hidden administrator profile, complete with the flag!

The profile was hidden from the front-end navigation, but the backend served it anyway as long as you requested ID `0`.

This was a classic demonstration of an insecure direct object reference (IDOR). If an application serves profiles simply by incrementing an ID without checking permissions, access controls are broken.

**Lesson learned:** Hiding a link from the menu does not make a page secure. Applications must verify authorization every time a resource is requested.

## 10. Web Security: Template Terror

Next came **Template Terror**, a challenge that introduced us to Server-Side Template Injection, commonly known as SSTI.

The description gave us a useful warning: the application rendered user input using a template engine, but not every input was safe to render.

We started testing how the application processed input, looking for signs that the template engine was evaluating our expressions rather than treating them as ordinary text.

Once we saw that our math payloads were being evaluated, we explored the server environment. That led us directly to the application's `app.py` source file, giving us a clean view of the backend code and the flag.

Seeing SSTI in action was eye-opening: instead of just interacting with the webpage, we were directly querying the server environment.

**Lesson learned:** Never blindly trust user input inside template engines. A small oversight in input handling can expose the entire backend file system.

## 11. Miscellaneous: The 67 Challenge

Now this one deserves a special mention!

**The 67 Things** was one of the biggest headaches of the entire competition for us. We spent a considerable amount of time trying to figure out what the creator had done.

At one point, we started searching for similar puzzles and came across an old Reddit thread where someone shared a similar challenge. That gave us the exact spark we needed.

We mapped `6` to `0` and `7` to `1`, turning the sequence into binary. Then we converted that binary stream into ASCII text.

And there it was: the hidden message and our flag!

The breakthrough came the moment we stopped repeating the same tests and looked for how others had approached similar encodings.

**Lesson learned:** When you hit a wall, step back and change your approach. Researching similar challenges and recognizing binary patterns under disguise can unlock the entire puzzle.

## 12. What We Learned Along the Way

Looking back, the competition was much more than a race to collect flags. Every category challenged us in a different way:

- **Reverse engineering:** We learned to inspect obfuscated code and see what a program was actually executing by letting it print its own payload.
- **Cryptography:** We explored Caesar ciphers, cascaded Base64 decoding, and weak XOR encryption.
- **Digital forensics:** We parsed through raw hexadecimal dumps, metadata fields, and hidden payloads.
- **Steganography:** We discovered how data hides inside the least significant bits of image color channels.
- **Web security:** We explored hidden profile IDs, broken access controls, and server-side template injection.
- **Binary analysis:** We learned how two recurring digits can hide an entire binary stream.
- **Research and problem-solving:** We saw firsthand the value of trying new tools, studying write-ups, and pivoting when an approach stalled out.

One thing became clear throughout the weekend: complicated-looking problems don't always need complicated solutions.

Sometimes printing an `exec` string solves the problem. Sometimes an image channel holds the key. Sometimes an obscure string of numbers is just binary in disguise. And sometimes, you just have to step back, grab a fresh tool, and try again!

## 13. Final Thoughts: More Than Just Flags!

Our CTF journey was filled with curiosity, trial and error, unexpected breakthroughs, and plenty of head-scratching along the way.

We solved challenges involving obfuscated Python, classical ciphers, multi-layer encoding, XOR keys, network dumps, image steganography, metadata, IDOR vulnerabilities, template injection, and binary conversion. We also tackled a binary-file challenge that we couldn't quite crack, but it added ten new tools to our toolbox.

The most satisfying moments were when a small clue finally clicked and the flag popped up on the screen. Those moments are why these competitions are so much fun.

We did not just hunt flags; we learned how systems work, how they break, and how to investigate them systematically.

**Every challenge had a lesson, every clue had a story, and every flag was another step forward in our cybersecurity journey!**
