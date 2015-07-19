---
title: Replacing .each_with_index with .map to streamline Ruby code
date: 2015-07-19 19:31 UTC
tags: Ruby
---

I recently had to write a method that would take an encrypted message and return a decrypted message using a key. The key was a hash of numeric codes and words like this.

~~~ruby
  { "1" => "secret", "2" => "message"}
~~~

I wanted to write a method that would look at each numeric code in the encrypted message and find its decrypted word in the key. I was familiar with using <code>.each</code> on arrays to perform actions on a bunch of elements, so I decided to convert the encrypted message into an array of numeric codes, replace each of the codes with words, and convert the array back into a string.

~~~ruby
  class Cipher
    @@key = {"1" => "secret", "2" => "message"}

    def self.decrypt_message(message)
      message = message.split(' ')
      message.each_with_index do |word, index|
        message[index] = @@key[word]
      end
      message = message.join(' ')
    end
  end
~~~

The method accomplished what I wanted it to do. When I called <code>Cipher.decrypt_message("1 2")</code>, it returned "secret message."

This was great, but it seemed like too many lines of code for such a simple task. I remembered seeing a <code>.map</code> method before, but I wasn't sure exactly what it did. I took a look at the Ruby documentation for array, and I figured out how to replace <code>.each_with_index</code> in my method with <code>.map</code>.

~~~ruby
  class Cipher
    @@key = {"1" => "secret", "2" => "message"}

    def self.decrypt_message(message)
      message = message.split(' ')
      message.map!{ |word| @@key[word] }
      message = message.join(' ')
    end
  end
~~~

With <code>.map</code>, I learned I could replace an element in an array in place without having to remember that element's index. I used <code>.map!</code> so that the array of words would replace the array of numeric codes, rather than returning a copy.

Then I did a little more refactoring.

~~~ruby
  class Cipher
    @@key = {"1" => "secret", "2" => "message"}

    def self.decrypt_message(message)
      message.split.map!{ |word| @@key[word] }.join(' ')
    end
  end
~~~

By chaining the methods I wanted to call on the encrypted message together and removing the optional parameter from <code>.split</code>, I reduced my method down to one short line.
