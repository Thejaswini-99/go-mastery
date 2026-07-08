How does hashmap's works internally?

A HashMap stores key-value pairs. A hash function converts the key into a bucket number. The bucket stores one or more key-value pairs. If multiple keys map to the same bucket, a collision occurs. When buckets become too full, the map resizes by creating more buckets and rehashing the existing keys.


So whay we need hashmaps:--> for speed. if we have millions of users stored we can't go and search linearly like where does each user stored instead of that we had buckets like 1 to. 100 users stored in the bucket 1 and 200 to 300 stored in the bucket 2 so searching becomes fast simply like indexing in the db

Hashfucntion: hashfunction that takes the key as a input and computes the hashvalue(bucket) where the key should be stored
For example:
Key = 42 no of buckest =10 
Hash Function
  42%10==2
Bucket 2


Collision:
Collision occurs when two or more different keys are mapped in the same bucket by the hash function
Example:
12 % 10 = 2
22 % 10 = 2
32 % 10 = 2

All of these goes into the bucket 2

---> so map resizes it self like i mena it can increases the number of buckets .Go measures something called Load factor

Load Factor
It is the average number of elements stored per bucket.
Load Factor tells how full a HashMap is.

Formula:
Load Factor = Number of Elements / Number of Buckets

Purpose:
• Reduce collisions.
• Decide when the HashMap should grow.
• Maintain fast lookup.

Higher Load Factor
↓
More collisions
↓
Slower lookup
↓
Resize the HashMap

And also there is threshold value set to each map if that thrshod value is more then hashmap resizes



Suppose Bucket 3 already contains 8 key-value pairs.

Now a 9th key hashes to Bucket 3.

What will Go do? -- it creates the overflow bucket to store the values 


Why is a Go map not thread-safe?

A normal Go map has no built-in synchronization. Multiple goroutines modifying the map simultaneously can corrupt its internal bucket structure, especially during insertions or resizing. To prevent silent memory corruption, the Go runtime detects concurrent writes and panics. For concurrent access, we should use synchronization such as sync.Mutex, sync.RWMutex, or sync.Map depending on the use case.