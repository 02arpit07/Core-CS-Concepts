# Thread Safe Code
Code that is safe to be called by multiple threads is thread safe code. A thread safe code does not have any race conditions.

#### Local Variables
As local variables are stored in a thread's own stack, they cannot be shared with other threads. This makes local variables thread safe.

#### Local Object References
If an object created locally never escapes the method it was created in, it is thread safe.

#### Object Member Variables
If two threads try to update member variables of same instance then the code is not thread safe as member variables are stored on heap. If two threads use two different instances, then the code is thread safe.
