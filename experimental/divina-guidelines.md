# Advanced Divina guidelines

## Guidelines relative to presentation hints

### Expression of continuity between consecutive resources

Continuity is expressed by the concatenation of two adjacent resources in the reading direction. 

As an example, if the reading direction is `ltr` (resp. `tbb`), two adjacent images are "glued" side by side (resp. one on top of the other).

## Guidelines relative to transitions

### Transitions applied on publications where continuous=true.

*This guideline is still under discussion by the WG*

Transitions should still be honored if `continuous=true`. This can be interpreted as a local discontinuity in the image flow. 

### Transitions applied on publications where overflow=scrolled

Continuous gestures should be disabled by the User Agent while a transition unfolds.

If a discontinuous gesture (like a "tap") is made during a transition, then its effect depends on the relative directions of the two movements:

* If they have the same direction (forward and forward, or backward and backward), the transition should be forced to its end - but no additional page change should be performed.
* If they have contradictory directions, then the transition should be cancelled and the publication brought back to the transition’s start or end, with an additional page change in the direction of the gesture.
 
Example 1: if a forward transition is unfolding and the user taps to go backward, the story will move back to the previous page (possibly playing the corresponding backward transition if there is one for the previous page).

Example 2: if a backward snap point jump is unfolding and the user taps forward, the story will jump to the next start point and then either automatically jump to the next one (if there is one) or trigger a page change (if there is none).

## Animations

*This guideline is still under discussion by the WG*

If the transition is an animation (either a sequence of images or a video file), its `fit` value should be inherited from the parent resource (i.e. the target resource for a forward transition and the source for a backward one) and it should be `clipped`.

## Duration of transitions 

In case the transition type is `animation`, a video `file` is defined but no duration is specified, then the video should play entirely (which goes further than saying that the transition duration should be that of the video: if the video lags, it should still reach its end before the next image appears). 

If a duration is specified which is shorter than the video duration, the video is cut before its end. If the duration is longer, the transition is ended after the video has been played entirely. 

Apart from the video case, if no `duration` is specified, the total duration of the transition is chosen by the client application. 
