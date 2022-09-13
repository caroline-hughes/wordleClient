Client that plays Wordle using an INET STREAMing socket, with the option to specify a port and the use of an ssl socket.

To use, run the script from the command line with the form:

```
$ ./client <-p port> <-s> <hostname> <Northeastern-username>
```

If not specified, the port will be 27993 and the hostname will be "proj1.3700.network".

My high-level approach to completing this assignment:
1. Re-learn/learn the basics of python3 scripts, argument parsing, etc.
2. Complete logic for sending the proper "hello" message. Abstract this socket.send() logic to be used for sending any message.
3. Complete logic to listen for the "start" response. Abstract this socket.recv() logic to be used for receiving any message.
4. Implement one loop of guessing (with a hard-coded test word) and receiving proper response from the server.
5. Write logic for adjusting the words list each guess based on the given marks (see guessing strategy below).
6. Implement complete loop for guessing until "bye" type is received, returning the flag from that response.
7. Researching how ssl python library works, and utilizing it to add tls support.

A challenge:
I would say the hardest part of this assignement for me was understanding how sending and receiving bytes over the socket worked. For a while, it was hard for me to test my client easily, because the try block of my receieveMessage function was running for ~2 minutes each time. Ultimately, changing from "while True" (and break-ing once !data) to "while '\n' not in buffer" (stopping at the first sight of a newline character, with the knowledge that the message has ended), fixed this problem, and I was able to test via the command line much easier.

Guessing strategy:
My runGuessLoop function handles the guessing for my client. Initially it receives the wordslist as an array, and it will trim the wordslist each time a new response to a guess has been received. Essentially, for each previous guess and its marks array, we traverse each character of that guess in addition to the mark associated with that character. 
- If the char corresponded to a 0 or 1, I filter the wordslist by words without that char in that index.  
- If the char corresponded to a 1, I filter the wordslist by words which contain that char somewhere.
- If the char corresponded to a 2, I filter the wordslist by words which contain that char at that index.

Testing:
Command line print statements were my primary form of testing and debugging my functionality. At each step of implementation, I was using print statements to see things like
- the command line arguments that were being used to instantiate the socket
- the format of the messages I was sending and receiving, 
- the number bytes being sent or received and added to buffer, 
- the progression of my guesses based on the wordlist filtering logic,
- which try catch blocks were catching errors