# Java Blocking Queue

Blocking Queue is an interface in Java which provides blocking functionality in addition to the queue's basic functionality. It enables blocking based on the fact that if the queue is empty or full. A thread trying to add an element to a queue which has already reached the capacity is blocked until some other thread removes an element from the queue. Also it blocks a thread trying to delete an element from an empty queue until some other thread inserts an item to the queue.

## Implementations of BlockingQueue interface in Java
 - LinkedBlockingQueue
 - ArrayBlockingQueue
 - PriorityBlockingQueue
 - SynchronousQueue
 
All the methods of this interface are thread-safe and all methods are atomic in nature. Internally they use locks and other form of concurrency control.

## Example of BlockingQueue in Java

#### Problem Statement
There are a number of candidates coming for an interview. The interview room(Queue) can only accomodate a set number(e.g. 3) of candidates at a time. There is a scheduler(```InterviewScheduler```) which acts as a producer in our case to schedule the interview and there is a processor(```InterviewProcessor```) which acts as a consumer to conduct the interviews. The consumer removes the candidate from the interview room after conducting an interview making space for new candidates to come.

#### Scheduler Implementation
```
import java.util.concurrent.BlockingQueue;

public class InterviewScheduler implements Runnable {
	
	private BlockingQueue<String> queue;
	
	public InterviewScheduler(BlockingQueue<String> queue) {
		this.queue = queue;
	}

	@Override
	public void run() {
		System.out.println("10 candidates have arrived");
		for (int i = 1; i <= 10; i++) {
			String message = new String("Candidate " + i);
			try {
				queue.put(message);
				System.out.println("Interview scheduled for " + message);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
		System.out.println("Interviews scheduled for all candidates");
		
		String message = new String("Stop");
		try {
			queue.put(message);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}
```

#### Processor Implementation
```
import java.util.concurrent.BlockingQueue;

public class InterviewProcessor implements Runnable {
	
	private BlockingQueue<String> queue;
	
	public InterviewProcessor(BlockingQueue<String> queue) {
		this.queue = queue;
	}
	
	@Override
	public void run() {
		try {
			String message;
			Thread.sleep(5000);
			while (!(message = queue.take()).equals("Stop")) {
				System.out.println("Interview Completed for " + message);
				Thread.sleep(5000);
			}
			System.out.println("Interview completed for all candidates");
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}
```

#### Interview Service
```
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;

public class InterviewService {

	public static void main(String[] args) {
		BlockingQueue<String> queue = new ArrayBlockingQueue<String>(3);
		
		InterviewScheduler producer = new InterviewScheduler(queue);
		InterviewProcessor consumer = new InterviewProcessor(queue);
		
		new Thread(producer).start();
		new Thread(consumer).start();
	}
}
```

#### Output
```
10 candidates have arrived
Interview scheduled for Candidate 1
Interview scheduled for Candidate 2
Interview scheduled for Candidate 3
Interview Completed for Candidate 1
Interview scheduled for Candidate 4
Interview Completed for Candidate 2
Interview scheduled for Candidate 5
Interview Completed for Candidate 3
Interview scheduled for Candidate 6
Interview Completed for Candidate 4
Interview scheduled for Candidate 7
Interview Completed for Candidate 5
Interview scheduled for Candidate 8
Interview Completed for Candidate 6
Interview scheduled for Candidate 9
Interview Completed for Candidate 7
Interview scheduled for Candidate 10
Interviews scheduled for all candidates
Interview Completed for Candidate 8
Interview Completed for Candidate 9
Interview Completed for Candidate 10
Interview completed for all candidates
```

As we can see that only three candidates are scheduled i.e. added inside the queue at a time and whenever an interview is completed for a candidate a new candidate is added to the queue.
