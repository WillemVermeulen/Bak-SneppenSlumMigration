<head>

 <script

 src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js">
 </script>
 <script>

 $(document).ready(function()
 {
     $(".gif_container").click(
         function()
         {
           var src = $(this).children(".gif").attr("src");
           if(src.includes("png")) {
            $(this).children(".gif").attr("src", src.replace(/\.png$/i, ".gif"));
            $(this).children(".overlay").hide();
            $(this).children(".play_button").hide();
           } else {
            $(this).children(".gif").attr("src", src.replace(/\.gif$/i, ".png"));
            $(this).children(".overlay").show();
            $(this).children(".play_button").show()
           }
         });

  $(".copyright").html("This implementation was built by Nicky Kessels, Maarten van der Sande, Wessel Klijnsma and <a href=\"https://github.com/WillemVermeulen\">Willem Vermeulen</a>.");
 });

 document.getElementById("project_title").innerHTML = "Complex Systems Simulation";
</script>

<link rel="stylesheet" type="text/css" href="http://slum.life/style.css">
</head>

## A 2D Bak-Sneppen Slum Migration Model

Slum migration is a phenomenon mostly studied by social sciences and a topic difficult to quantify and make tangible. By modifying a 2D [Bak-Sneppen model](https://en.wikipedia.org/wiki/Bak%E2%80%93Sneppen_model), often used to model evolution, we attempt to generate the migration patterns. We show that by using only a few rules in a simple cellular automaton complex migration patterns start to occur.

<img src="https://upload.wikimedia.org/wikipedia/commons/b/ba/Slums_in_Caracas%2C_Venezuela.jpg" width="100%"/>

## The Basic Model

The basic version of the Slum Migration Model has only some slight modifications with respect to the basic 2D Bak-Sneppen Model. First of all it has empty cells, whereas the original method did not have those. These cells are needed to migrate cells to a location where they might get a higher fitness. The interactions with the neighbours remain intact, but a neighbouring migrating cell results in a percentual decrease of the fitness instead of a randomised new fitness.

Each time step, the following steps are taken:
<ol type="A">
<li>Select the cell with the lowest fitness within the model.</li>
<li>Select a random empty cell and give this cell a new fitness using the formula mentioned below.</li>
<img src="https://latex.codecogs.com/gif.latex?new\_fitness&space;=&space;old\_fitness&space;&plus;&space;|\mathcal{N}(0,&space;\frac{1&space;-&space;old\_fitness}{3})|" class="latex"/>
<li>Empty the previously selected cell at A. and lower the cells within the <a href="https://en.wikipedia.org/wiki/Von_Neumann_neighborhood">von Neumann Neighbourhood</a> by a certain factor.</li>
</ol>

<img src="http://slum.life/images/bak-sneppen_expl.png" width="100%" class="no-border"/>

## Ages

When visualizing the simulation, the values plotted are the ages. These are defined as the number of timesteps that a person lives in a certain cell. This means that the age of a cell is incremented during each timestep that someone lives in certain cell and is set to zero when someone moves to another cell.

<div class='gif_container'>
<img class='gif' src="http://slum.life/videos/slum_barebones.png" width="100%"/>
<div class="overlay"></div>
<div class="play_button">&#9658;</div>
</div>
<span class="description">A simulation of the basic Slum Migration Model.</span>

<div class='gif_container'>
<img class='gif' src="http://slum.life/videos/slum_barebones2.png" width="100%"/>
<div class="overlay"></div>
<div class="play_button">&#9658;</div>
</div>
<span class="description">A simulation of the basic Slum Migration Model further in time and larger grid.</span>


<div class='gif_container'>
<img class='gif' src="http://slum.life/videos/barebones_fitness.png" width="100%"/>
<div class="overlay"></div>
<div class="play_button">&#9658;</div>
</div>
<span class="description">A simulation of the basic Slum Migration Model showing fitness values.</span>
## Avalanches

Just like in the [Bak-Tang-Wiesenfeld model](https://en.wikipedia.org/wiki/Abelian_sandpile_model) a single changing cell might induce a cascade of changing cells (avalanche). A single person moving away might inspire its neighbours to move away, and they again might inspire theirs, etc. To quantify this behaviour we measure the avalanche size. As an avalanche starts the fitness of the starting cell is set as limit for the avalanche. As long as the consecutive mutations are below this value its still part of the same avalanche, otherwise another avalanche started.

The distribution of the avalanche sizes follows a power law:
<img src="https://latex.codecogs.com/gif.latex?f(x)&space;=&space;ax^{-k}}" class="latex"/>

<img src="http://slum.life/images/avalanche_sizes.svg" width="100%"/>

### Critical State

The plot has two parts, the power law and the fat-tail. In the beginning of the simulation the majority of the avalanches are small and proportional to the initial perturbation, explaining the power-law. However as the simulation progresses the system reaches the critical state and the avalanches get bigger and bigger, resulting in the fat tail of the simulation.


## Influence of Slum Parameters

<img src="http://slum.life/images/emptypercent10x20000.svg" width="49%" class="no-border"/>
<img src="http://slum.life/images/slumsize20x25000.svg" width="49%" class="no-border"/>
<span class="description">The effect of the empty percentage of cells and slum size on the K of the powerlaw distribution of avalanche sizes. Each percentage was tested 10 times for 20000 time steps, each slum size 20 times for 25000 time steps.</span>

The slum size used and the empty percentage of cells within a slum often have negligible effects. Only when the empty percentage of cells becomes very large (95%), the variance of the K value becomes a lot higher than with lower precentages of empty cells. This can be explained by the fact that filled cells should have neighbours to cause avalanches. Before all cells are clustered it takes some time - which means that the warming up period can vary a lot, depending on the initial spread.  

Another effect can be seen with very small slum sizes. In this particular cases avalanches seem to encounter themselves through the periodic boundaries.

<img src="http://slum.life/images/decreasefactor30x20000.svg" width="49%" class="no-border middle"/>
<span class="description">The effect of the decrease factor of the neighbouring cells on the K. Each factor was tested 30 times for 20000 time steps.</span>

The decrease factor seems only to be having an effect on the variance, but not on the mean of the K-values. Once the decrease factor reaches 1, there can only be one avalanche in the whole system, which makes the K fitting negligible.

## More Slums
<img src="https://upload.wikimedia.org/wikipedia/commons/4/44/Dharavi_India.jpg" width="100%"/>

So far we modelled a system with only one slum, however in reality often a network of slums exists. We expanded our model so that it can contains multiple slums with migration between them. 

### A Better Slum Selection Strategy
In real life, people won't move to a random slum. They will probably have a preference for slums where people are happier than they currently are. Instead of moving randomly, their strategy would move to a slum with happier people. A third more extreme moving strategy would be to pick the slum with the happiest people which has room for a new person.

The effects of these three different moving strategies on the K of the powerlaw distribution are shown in the figure below.

<img src="http://slum.life/images/strategy10x20000.svg" width="100%" class="no-border"/>
<span class="description">The effect of slum selection strategy on the K of the powerlaw distribution of avalanche sizes. Each strategy was tested 10 times for 20000 time steps.</span>

<img src="http://slum.life/images/nrslums10x20000.svg" width="100%" class="no-border"/>
<span class="description">The effect of the number of slums on the K of the powerlaw distribution of avalanche sizes. The total number of cells within the simulation remained the same. Each size was tested 10 times for 20000 time steps. The migration strategy was 'mild preference'.</span>

<div class='gif_container'>
<img class='gif' src="http://slum.life/videos/slum_multiple.png" width="100%"/>
<div class="overlay"></div>
<div class="play_button">&#9658;</div>
</div>
<span class="description">Animation of three slums over time, with the corresponding avalanche sizes, barrier distribution and slum densities.</span>

<div class='gif_container'>
<img class='gif' src="http://slum.life/videos/slum_network.png" width="100%"/>
<div class="overlay"></div>
<div class="play_button">&#9658;</div>
</div>
<span class="description">Animation of the traffic between six slums per 50 iterations. We see first see a fully connected graph, which indicates we didn't reach the critical state yet. At around 4500 iterations we reach the (start of the) critical state and we see clear patterns of some slums attracting a lot of influx, while others only have outflux.</span>

## The Destination

### The Optimal Location within a Slum
When moving to a new spot, people don't move to a random cell. People mostly move to spots where people already live. Therefor the probability of someone moving to a certain empty cell within a given slum is calculated as follows:

<img src="https://latex.codecogs.com/gif.latex?p&space;=&space;\frac{\&hash;neighbours^2&space;&plus;&space;0.01}{p_{total}}" class="latex"/>


## Emergence of New Slums

Even though there is an interaction between slums, one important part of slum behaviour is still missing: the emergence of new slums. In real situations we see new slums appear. The introduction of slums in the Slum Migration Model happens when all slums are at least 50% full and at least 98% of the busiest slum is filled with cells. When a slum is introduced, there is a strong influx into the new slum, until the same population density is reached in all slums. These effects can be seen below.

<div class='gif_container'>
<img class='gif' src="http://slum.life/videos/slum_new_slum.png" width="100%"/>
<div class="overlay"></div>
<div class="play_button">&#9658;</div>
</div>
<span class="description">Animation of two slums over time. At around the 3000th iteration a third slum emerges. This causes a strong influx of population into the new slum. Once the three slums reach the same density the old pattern of oscillating densities emerges again.</span>

## An Increasing Population

An increase in the number of slums implicity means that there is a decrease in the number of filled cells within the slums. This is not realistic. In real life slums experience a population growth, because there is a birth surplus and people are still drawn from the countryside towards the bigger cities. In our model people are added in exponentially distributed intervals based on the maximum slum density.

<div class='gif_container'>
<img class='gif' src="http://slum.life/videos/slum_new_person.png" width="100%"/>
<div class="overlay"></div>
<div class="play_button">&#9658;</div>
</div>
<span class="description">Animation of two slums over time. At around the 1000th iteration a third slum emerges and at around the 34000th iteration a fourth slum emerges. In this simulation people are added.</span>

## What's next?

### Validation

<img src="https://ai2-s2-public.s3.amazonaws.com/figures/2016-11-08/ee78c1fd6243a874411518b5180c87917ed54bf6/6-Figure2-1.png" width="100%"/>
<span class="description">[Kit, Oleksandr and Matthias Lüdeke. “Automated detection of slum area change in Hyderabad, India using multitemporal satellite imagery.” (2013).](https://www.semanticscholar.org/paper/Automated-detection-of-slum-area-change-in-Hyderab-Kit-L%C3%BCdeke/ee78c1fd6243a874411518b5180c87917ed54bf6)</span>

A logical step would be to check how the model compares to real data. There are first hints that the avalanches also follow a power law, and the densities of slums grow sinusoidal. This has yet to be properly tested still however.

### Define Fitness

Happyness (fitness) is now defined as a number between 0-1, but has no meaning in the real world. A logical next step would be to replace this number with a real-life measurable number and see if it the model holds. This then can also be compared to the model, and maybe even predications can be made.
