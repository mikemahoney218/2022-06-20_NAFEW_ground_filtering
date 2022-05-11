# Slide 1
Hi everyone! I'm Lucas Johnson, and I'm excited to talk to you all today about our study on ground noise filtering when modeling aboveground biomass with LiDAR data. 

# Slide 2
To start off, I should introduce myself! I'm currently a PhD candidate at the State University of New York College of Environmental Science and Forestry, working on mapping forest aboveground biomass across the state of New York to try and track carbon sequestration.

# Slide 3

See, in 2019 New York State passed its landmark climate legislation, the CLCPA. As part of that, we're working to hit net zero emissions statewide by 2050. Now, a huge part of that is going to be in reducing emissions through renewable energy sources, but we have to acknowledge that there are parts of the economy that we won't be able to decarbonize in the next thirty years. And so to make up the difference, we're looking to manage our lands and forests so that they're continually sequestering at least the same amount of carbon dioxide as we're releasing.

But in order to manage the carbon sequestered by our lands and forests, we need to measure the carbon sequested by our lands and forests. And New York State is a big place, which makes it hard to measure.

# Slide 4

We're very lucky to have a rich source of forest inventory data from the USDA's Forest Inventory and Analysis program -- the FIA -- which goes out and measures more than 5,000 inventory plots statewide. But these plots cover relatively small footprints, are only measured on a rolling seven year basis, and due to privacy concerns are really only used to estimate forest biomass on the county and state levels. That makes it hard to use the FIA to actually plan active management or to monitor sequestration.

# Slide 5

And so what a lot of other groups have tried to do is to fill in the gaps using LiDAR data. For anyone not familiar, LiDAR is a bunch of point measurements of terrain height that you collect by shooting a laser at the ground, and by strapping that laser to an airplane you can collect this sort of data over a large extent much faster and cheaper than you can measure more field plots. And so by computing summary statistics describing these point clouds we can get a description of what the ground cover _looks_ like at a site. If we combine those variables with FIA measurements, we can build models to extrapolate FIA biomass estimates outside of the areas that FIA actively measures. And a lot of groups have had success doing this for most of New England, some of Maryland and Pennsylvania, and regions in the Pacific Northwest. 

# Slide 6

And as luck would have it, we've got LiDAR in New York that covers a bit more than half of the state. So, over the past few years we've been developing these models for New York State, and actually have our first publication of these models in review at Environmental Research Letters right now -- I'll send a link to the preprint in the chat at the end of the talk. So, the first thing that we had to do was compute our summary statistics.

# Slide 7

Now, these summary statistics include things like the mean height of our points and other distributional traits like that. This slide here is showing the cross section of some of the LiDAR included in our models -- the red points are the highest elevations, the blue are the lowest. And this area is continuously forested, we can tell that from imagery, but you'll notice that there's still a lot of points that are getting through the trees and giving us the height of the ground here.

# Slide 8

Now what a lot of other biomass models do, is they throw out all of those "ground points" -- what we call "ground noise filtering". The idea is pretty straightforward -- if you're interested in measuring the biomass in trees, and these points hit the ground instead of the trees, then you aren't interested in these points. But as we were looking through the literature to figure out why people did this, we realized that no one had ever looked at how this actually impacts your model. 

# Slide 9

A lot of biomass modeling studies cited other biomass modeling studies to explain why they filtered out their ground noise, but if you follow the citation chain all the way back you find this pair of studies from the late 90s that really introduced the practice. Mats Nilsson in 1996 was using helicopter-borne LiDAR to try and estimate tree heights, and set any point below 2 meters to 0. And in another study modeling tree heights, Erik Naesset in 1997 threw out all points below 2 meters. And when you're measuring tree heights, that makes a lot of sense, because those returns below 2 meters aren't representative of the maximum height of a tree.

But at some point the field moved from modeling tree heights to stand biomass, and it seems like ground noise filtering stuck around because it was something we already did. But when you're estimating the biomass of a stand, it might be useful to know how much of the stand is open enough that the laser can actually get through the trees in order to hit the ground. The amount of unoccupied space might be just as relevant as the sizes of the trees themselves.

# Slide 10

So, we set out to find out: does filtering ground noise actually improve our biomass models?

# Slide 11

Now, one thing to mention is that the LiDAR data we're working with spans a lot of different regions. 

# Slide 12

A lot of New York State is post-agricultural, with croplands starting to return to forest and a good amount of rural development. And at the same time, the Adirondack Park in the North is the largest protected area in the lower 48 states. So we've got a large variety of landscapes represented in our data. 

And so our second objective was to identify if the impact of filtering was associated with any landscape metrics. 

# Slide 13

Specifically, if ground noise was helping us identify more open stands and empty spaces, we wondered if ground noise filtering would have a bigger impact in patchier and less forested landscapes.

# Slide 14

So we went ahead and filtered our LiDAR data four different ways, removing points that were at 0 meters and below, point 1 meters and below, 1 meter, and 2 meters. We then fit random forests to each of those, plus the original unfiltered data set, using an automated machine learning approach to make sure we weren't accidentally biasing the results by doing a better job tuning one model over another. And then we compared the performance of these models against each other, both statewide and in relation to landscape metrics like patchiness and forest cover.

# Slide 15

And perhaps not surprisingly, we found that truncating our data by getting rid of the lowest points was highly impactful to our variable distributions. This graph is a selection of the variables we include in our models, and as you can see there's a lot of movement between our unfiltered control data set, the grey, and the colorful filtered data. And, also maybe as expected, the difference between our unfiltered predictors and the filtered versions increases as the filtering gets more intensive, as we use a higher threshold. And generally speaking, the kurtosis of these variables increases as the filtering gets more intensive; we're really reducing the amount of information available to our models by filtering this way.

# Slide 16

So then we fit models to these predictor sets and then assessed their accuracy against FIA measurements through leave-one-out cross validation. And what we found is that, across New York, our models generally got worse the more intensively we filtered. Error metrics like mean absolute error and root mean squared error increased, while agreement metrics like R2 decreased as we filtered using higher thresholds. 

Now one exception here is that our models were at their least accurate when we used the 0.1 meter threshold. And we really don't have a great explanation for that right now. But what we can say is that the unfiltered model was consistently the most accurate for New York, and that filtering decreased our model accuracy overall.

So that's our first objective done: ground noise filtering does not help models of forest aboveground biomass. If anything, it hurts it.

# Slide 17

Now for our second objective, we looked into how changes in accuracy were associated with things like edge density, patch density, and forest cover. And to do that we took each one of these LIDAR coverages as its own unit and calculated its landscape metrics, as well as how model accuracy inside its borders changed with the different filtering heights. 

# Slide 18

And what we found is that yes, the impacts of filtering are much more notable in the more fragmented and less forested coverages. So this graph is showing the change in RMSE associated with a 1 meter filtering level -- so not that weird 0.1 meter level, but the next higher one where it comes back down. So higher RMSEs mean more of an impact from filtering. And what we found is that fragmentation metrics like edge density and patch density were generally positively correlated with RMSE, so patchier landscapes were more impacted by filtering overall. And similarly, areas with less forest cover were more impacted by the models as well. 

Now something interesting here is that, in the most forested and least patchy coverages, filtering had an almost 0 or even slightly positive impact on performance. So one caveat on our results is that, if you're only interested in mapping a contiguously forested area, ground noise filtering might help improve your models by a tiny bit. But the improvements are so small, and not guaranteed, so we think filtering should really be a careful decision and not the sort of standard pre-processing procedure it's become for a lot of biomass models.

# Slide 19

So, to wrap up, we found that filtering out the lowest points from LiDAR data doesn't help and often hurts models of forest aboveground biomass. And in particular, it tends to hurt patchier landscapes, which are often the things being modeled in current wall-to-wall mapping studies. So, we recommend that if you're looking to build these sorts of models, you don't filter your data unless you've got a very good reason.

# Slide 20

And that's my time! I'll post links to the preprints for both this study and our biomass modeling study in the Zoom chat. Thanks so much for listening!



