Download Link: https://assignmentchef.com/product/solved-cs3210-assignment-1-particles-movement-simulator-using-parallel-programming
<br>
This assignment is designed to enhance your understanding of parallel programming with sharedmemory (OpenMP) and GPU programming using CUDA. You will apply parallelization models you learned in class to solve a real world problem.

Problem Scenario

Simulating the movement of particles is one of the large-scale problems of current interest in climatology, plasma physics, fluid dynamics and celestial mechanics. You are tasked to implement and parallelize a simulator of particles in a 2D space.

Figure 1 shows a 2D space that contains particles. The particles are located on a square surface of a given size. They are all assumed to have the same mass and size (radius <em>r</em>), and they move with given velocities. The position is defined using (<em><sub>x,y</sub></em><sub>) </sub>coordinates of the center of the particle, expressed from the left lower corner of the square. The particles have collisions with other particles and with the walls of the surface. The collisions are elastic.

Figure 1: Particles on a square surface

Your task is to write parallel programs in (i) OpenMP and (ii) CUDA to simulate this movement of particles in the square. The simulation runs for given time, in steps. You will need to provide two modes of running this simulation:

<ul>

 <li>Correctness mode: at every step, you need to output the the position of every particle.</li>

 <li>Speed mode: you need to output the position of every particle before the first step and after the last step.</li>

</ul>

Given an initial position of the particles, the final position should be the same for both modes.

1

The collisions with the walls and other particles are modeled as elastic collisions where the momentum and kinetic energy are conserved (details to be found in the next sections).

<strong>One collision per step for each particle is considered. </strong>The collisions with the walls and other particles may happen during every step (time unit) of the simulation. As a simplification, each particle is involved in at most one collision during each step (all following collisions can be ignored). There can be multiple collisions happening during the same step, but no one particle is involved in more than one collision. You need to use the velocities from before and after the collision to compute the final position of the particle after each time step. In general, the step is fine enough (particles are slow enough and not too many) such that there would be less than one collision for each particle during each step. However, the collisions might happen at any time during a step.

<strong>Only the earliest collisions during a step is considered. </strong>If there are multiple possible collisions with the wall or other particles during the same step, only the earliest collision will be considered (and others ignored). When collisions between two particles are ignored it means that they move beyond each other without their velocity and direction changing. They might overlap at the end of the step. In that case, the collision is computed in the next step from the overlapped positions. If a particle needs to collide with a wall as its second collision in a step, the particle stops just next to the wall (it will collide in the next step).

<strong>Multiple simultaneous collisions during a step. </strong>In the unlikely case that a particle is involved in two collisions exactly at the same time (in the step), the particles with the lowest index collide, and the other collisions are ignored. If a particle is involved in a collision with the wall and with another particle exactly at the same time (in the step), the particle collides with the wall, and the other collisions are ignored. <strong>This assignment is divided into two parts:</strong>

(i) OpenMP Implementation is Assignment 1 Part 1 due on 30 Sep, 11am

(ii) CUDA Implementation is Assignment 1 Part 2 due on 21 Oct, 11am

<h1>Inputs and Outputs</h1>

<u>Input file </u>strictly follows the structure shown below:

<ol>

 <li><em>N </em>– Number of particles on the square surface</li>

 <li><em>L </em>– Size of the square (in <em>µm</em>)</li>

 <li><em>r </em>– Radius of the particle (in <em>µm</em>)</li>

 <li><em>S </em>– Number of steps (<em>time<sub>units</sub></em>) to run the simulation for</li>

 <li><em>print </em>or <em>perf</em>– If word <em>print </em>appears, the position of each particle needs to be printed at each step. Otherwise <em>perf </em>should appear.</li>

 <li>Optional: For each particle, show on one line the following information, separated by space:

  <ul>

   <li><em>i </em>– the index of the particle from 0 to <em>N </em><sub>− 1</sub></li>

   <li><em>x </em>– intial position of particle index <em>i </em>on <em>x </em>axis (in <em>µm</em>)</li>

   <li><em>y </em>– intial position of particle index <em>i </em>on <em>y </em>axis (in <em>µm</em>)</li>

   <li><em>v<sub>x </sub></em>– initial velocity on the <em>x </em>axis of particle <em>i </em>(in <em>µm</em>/<em>time<sub>unit</sub></em>)</li>

   <li><em>v<sub>y </sub></em>– initial velocity on the <em>y </em>axis of particle <em>i </em>(in <em>µm</em><sub>/<em>time</em></sub><em><sub>unit</sub></em>)</li>

  </ul></li>

</ol>

If the initial location of the particles is not provided, you need to generate random positions and velocities for all particles. The positions should be values within the 2D surface <em>L </em><sub>× <em>L</em></sub>, while velocities should be in the interval  and . To show the direction of movement, velocities have positive and negative values. The particle <em>i</em>, the velocity  with and angle <em>α </em>with <em>x</em>-axis with . The velocity vector is

.

<h2>Sample Input</h2>

<sub>1000</sub>

20000

1

1000 print

<u>Output file </u>strictly follows the structure shown below:

<ol>

 <li>Print the positions and velocities of each particle in the beginning of the simulation. For each particle, show on one line the following information, separated by space:

  <ul>

   <li>0 – step 0 in the simulation</li>

   <li><em>i </em>– the index of the particle</li>

   <li><em>x </em>– initial position of particle index <em>i </em>on <em>x </em>axis (in <em>µm</em>)</li>

   <li><em>y </em>– initial position of particle index <em>i </em>on <em>y </em>axis (in <em>µm</em>)</li>

   <li><em>v<sub>x </sub></em>– initial velocity on the <em>x </em>axis of particle <em>i </em>(in <em>µm</em>/<em>time<sub>unit</sub></em>)</li>

   <li><em>v<sub>y </sub></em>– initial velocity on the <em>y </em>axis of particle <em>i </em>(in <em>µm</em><sub>/<em>time</em></sub><em><sub>unit</sub></em>)</li>

  </ul></li>

 <li>If <em>print </em>is used in the input file, at each step (time unit) <em>t<sub>u</sub></em>, your program should output the positions and velocities of each particle. The print should be done after the particle movement is computed for that step. For each particle, show on one line the following information, separated by space:

  <ul>

   <li><em>t<sub>u </sub></em>– the step in the simulation</li>

   <li><em>i </em>– the index of the particle</li>

   <li><em>x </em>– position of particle index <em>i </em>on <em>x </em>axis (in <em>µm</em>) at time <em>t<sub>u</sub></em></li>

   <li><em>y </em>– position of particle index <em>i </em>on <em>y </em>axis (in <em>µm</em>) at time <em>t<sub>u</sub></em></li>

   <li><em>v<sub>x </sub></em>– velocity on the <em>x </em>axis of particle <em>i </em>(in <em>µm</em>/<em>time<sub>unit</sub></em>) at time <em>t<sub>u</sub></em></li>

   <li><em>v<sub>y </sub></em>– velocity on the <em>y </em>axis of particle <em>i </em>(in <em>µm</em>/<em>time<sub>unit</sub></em>) at time <em>t<sub>u</sub></em></li>

  </ul></li>

 <li>At the end of the simulation, for each particle show on one line the following information, separated by space:

  <ul>

   <li><em>S </em>– last step in the simulation</li>

   <li><em>i </em>– the index of the particle</li>

   <li><em>x </em>– final position of particle index <em>i </em>on <em>x </em>axis (in <em>µm</em>)</li>

   <li><em>y </em>– final position of particle index <em>i </em>on <em>y </em>axis (in <em>µm</em>)</li>

   <li><em>v<sub>x </sub></em>– final velocity on the <em>x </em>axis of particle <em>i </em>(in <em>µm</em>/<em>time<sub>unit</sub></em>)</li>

   <li><em>v<sub>y </sub></em>– final velocity on the <em>y </em>axis of particle <em>i </em>(in <em>µm</em>/<em>time<sub>unit</sub></em>)</li>

   <li><em>p<sub>collisions </sub></em>– total number of collisions with other particles</li>

   <li><em>w<sub>collisions </sub></em>– total number of collisions with the wall</li>

  </ul></li>

</ol>

To avoid the errors associated with the floating pointing computations, use double precision floating point operations (double) for <em>x</em>, <em>y</em>, <em>v<sub>x</sub></em>, <em>v<sub>y</sub></em>. However, when you print the values to the file show only the first 8 digits after the decimal point (for example, use 10<em><sub>.</sub></em><sub>8<em>f </em></sub>for printf).

<h1>The Physics Engine</h1>

The collisions with the walls and other particles are modeled as elastic collisions where the momentum and kinetic energy are conserved.

When the particles collide with the walls of the square they reflect with the same velocity, in a different direction. When a particle collides with the horizontal wall, the velocity after the impact are modeled using the following vector: . When a particle collides with the vertical wall, the velocity after the impact are modeled using the following vector: . When particle collides with the corner of the square, both velicities on the x and y-axis are reversed:

For a collison with another particle, you need to compute the new directions and velocities for particles according the laws of physiscs for 2D elastic collision for 2 particles with <strong>equal masses</strong>. For example, Figure 2 shows a collision between two particles with velocities <em>v</em><sub>1 </sub>and <em>v</em><sub>2</sub>. You can see the normal unit (connecting the centers of the two particles) and the tangent unit (perpendicular on the normal) vectors: <em>u~n </em>and <em>ut~ </em>. The unit vectors are computed as follows:  and  and

orange:

Figure 2: Before 2D collision of two particles

Intuitively, at collision, the new velocities change as if the particles would hit against an imaginary wall located along the tangential vector. Figure 3 shows the velocity for both particles, with their components along the normal and tangential axis, and <em>x </em>and <em>y</em>-axis.

The tangential components of the velocities do not change. Hence the velocities on the tangential vector

after the collision are: <em>v</em><sub>1<em>t</em></sub>0 = <em>v</em><sub>1<em>t </em></sub>and <em>v</em><sub>2<em>t</em></sub>0 = <em>v</em><sub>2<em>t</em></sub>

The normal components of the velocities after the collision are as follows (particles have equal masses):

<em>v</em>1<em>n</em>0 = −<em>v</em>2<em>n </em>and <em>v</em>2<em>n</em>0 = −<em>v</em>1<em>n</em>.

Figure 3: After 2D collision of two particle

For further details on the physics engine, see the file titled <strong>2dcollisions.pdf</strong>.

<h1>Optimizing your Simulation</h1>

Note that it is easy to implement a simulation using a sequential algorithm. However, the challenging part is to come up with a parallel implementation that scales when increasing input size, number of threads and hardware capabilities. As such, you might need to try several approaches to parallelize the algorithm and several parallel implementations. You are advised to retain your alternative implementations and explain the incremental improvements you have done.

To analyze the improvements in performance, for a carefully chosen input, you should measure the execution time for increasing number of threads. You are advised to focus more in Part 2 on optimizing your implementation, and comparison between OpenMP and CUDA. You are allowed to change/improve your implementation for Part 1 for your Part 2 submission.





