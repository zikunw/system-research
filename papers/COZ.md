# COZ: Finding Code that Counts with Causal Profiling

COZ is a causal profiler that works on unmodified Linux binaries.

Introduces idea of **casual profiling**:
- Indicates exactly where programmers should focus their optimization efforts and quantifies their potential impact by running performance experiments.
- The result is calculated through *virtual speedup* (using the relative effect by slowing other threads down).

Problem with most of the profilers: **Code that runs for a long time is not necessarily a good choice for optimization.**

Example of where runtime profiling does not help (critical path shifted after optimization `a()`):

<img width="322" alt="Screenshot 2023-11-13 at 13 12 50" src="https://github.com/zikunw/system-group-research/assets/68682076/6d80732b-480b-4694-aeda-6ab04daec8b8">


Example of virtual speedup:

<img width="316" alt="Screenshot 2023-11-13 at 13 03 48" src="https://github.com/zikunw/system-group-research/assets/68682076/50daacfb-7cb2-4b9a-89f2-0b269a29393e">

Implementation details:
1. Ways to record progress point:
	1. Source-level progress points: modifying the source code
	2. Breakpoint progress points: Uses Linux `perf_event` API to set a breakpoint at the first instruction in a line specified in the profiler arguments.
	3. Sampled progress points: count the number of samples that fall within the specified line.
2. Virtual speedup is used randomly:
	1. Avoid program with phases.
	2. Avoid if impact of a particular line changes over time.
3. Controls inter-thread pausing using counters rather than POSIX signals to avoid high overheads.
4. Only collects samples and inserts delays in a thread while that thread is actually executing.
5. COZ forces a thread to execute all requried delays before it does anything that could block that thread or wake a suspended thread:
	1. If a suspended thread resumes execution because of another thread, the suspended thread should be credited for any delays inserted in the thread responsible for waking up.
