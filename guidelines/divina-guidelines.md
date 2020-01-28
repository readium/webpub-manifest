
## Guidelines for transitions

### Transitions applied on publications where continuous=true.

A transition disables any continuity between the current resource and the next, i.e. it locally applies a form of `continuous`=`false`. Therefore, a transition represents the only way of creating “breaks” in a webtoon (i.e. when continuous=true). However, do note that:

* This can only be done with a `transitionForward` (if continuous=true, any `transitionBackward` will be discarded, except if it happens to coincide with a `transitionForward`, i.e. if both transitions are properties of the same Link Object).
* The elements making up a “page” in such a paginated webtoon will necessarily be stitched together in the direction of the `readingProgression` (for example, you can’t have 2 images stitched together vertically if `readingProgression` = `ltr`).

### Transitions applied on publications where overflow=scrolled

Scrolling should be disabled by the User Agent while a transition unfolds.

If a discontinuous gesture is made during a transition, then its effect depends on the relative directions of the two movements:

* If they have the same direction (forward and forward, or backward and backward), the transition should be forced to its end - but no additional page change should be performed.
* If they have contradictory directions, then the transition should be cancelled and the publication brought back to the transition’s start or end, with an additional page change in the direction of the gesture.
 
Example 1: if a forward transition is unfolding and the user taps to go backward, the story will move back to the previous page (possibly playing the corresponding backward transition if there is one for the previous page).

Example 2: if a backward snap point jump is unfolding and the user taps forward, the story will jump to the next start point and then either automatically jump to the next one (if there is one) or trigger a page change (if there is none).

## Duration of transitions 

In case the transition type is `animation`, a video `file` is defined but no duration is specified, then the video should play entirely (which goes further than saying that the transition duration should be that of the video: if the video lags, it should still reach its end before the next image appears). 

If a duration is specified which is shorter than the video duration, the video is cut before its end. If the duration is longer, the transition is ended after the video has been played entirely. 

Apart from the video case, if no `duration` is specified, the total duration of the transition is chosen by the client application. 
