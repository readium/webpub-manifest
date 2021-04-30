# Transitions

The Transitions Module defines transition effects that may appear between resources in a Divina publication.

### transitionForward, transitionBackward

In the reading order of a publication, Link Objects <strong class="rfc">may</strong> contain the following additional properties: 

| Key   | Semantics | Type     | Values    | 
| ----- | --------- | -------- | --------- | 
| [transitionForward](#transitionForward-transitionBackward) | Describes the transition to be applied when moving FROM the previous resource TO current resource in reading order  | Transition Object | See [Transition Object](#the-transition-object)  | 
| [transitionBackward](#transitionForward-transitionBackward) | Describes the transition to be applied when moving FROM the current resource TO the previous resource in reading order  | Transition Object | See [Transition Object](#the-transition-object)  | 

Note: Keep in mind that a forward transition is placed on the TARGET resource of the transition.

### The Transition Object

This specification defines the following keys for this JSON object:

| Key  | Definition | Format | Required? |
| ---- | -----------| -------| ----------|
| [type](#type)  | Type of transition  | `cut`, `dissolve`, `slide-in`, `slide-out`, `push`, `animation` | Yes |
| [direction](#direction)  | Direction of a slide-in, slide-out or push transition  | `ltr`, `rtl`, `ttb`, `btt` | Yes if the type is slide-in, slide-out or push. |
| [sequence](#sequence)  | Sequence of images which create an animation | Array of URIs | No |
| [file](#file)  | Video file which creates an animation | URI | No |
| [duration](#duration)  | The duration of the transition in milliseconds | Integer | No |


#### type

| Value  | Definition | 
| ---- | -----------| 
| `cut` | the new resource immediately replaces the current one; this transition does not need a duration and is only useful when continuous=true, as a way to specify an explicit cut (“page change”) between two resources. | 
| `dissolve` |  the new resource appears above the current one, with opacity increasing from 0 to 1. | 
| `slide-in` | the new resource appears above the current one, and moves into the viewport to cover it. | 
| `slide-out` | the next resource is placed below the current one, which moves out of the viewport. | 
| `push` |  the next resource moves into the viewport while the current one moves out of it, revealing the next one below. | 
| `animation` | the next resource appears after playing a sequence of images or a video. |

If the transition type is `animation`, either a `file` or a `sequence` property <strong class="rfc">must</strong> be specified.

#### direction

| Value  | Definition | 
| ---- | -----------| 
| `ltr` | the new image comes from the left (whether the transition is forward or backward). | 
| `rtl` | the new image comes from the right. | 
| `ttb` | the new image comes from the top . | 
| `ltr` | the new image comes from the bottom. | 

Note: Usually, if the reading progression is ltr, forward transitions will be rtl. Also, the reverse of an rtl slide-in is an ltr slide-out.

#### sequence

Only used when the type is `animation`, the value of the `sequence` property is an array of Link Objects pointing to bitmap images displayed before the next resource appears.

Each image in the array is a frame displayed for a slice of `duration` divided by the number of images of the sequence.

#### file

Only used when the type is `animation`, the value of the `file` property is a Link Object pointing to a video to be played before the next resource appears.

#### duration

Duration (in ms) can apply to any type of transition. 


## Appendix A. Examples

*Example: This example features transitions. A slide-in btt from image1 to image2 with a backward slide-out ttb from image2 to image1; an image sequence from image2 to image3 with no backward transition; a video from image 3 to image 4 with no backward transition.*

```json
{
  "metadata": {
    "readingProgression": "ttb",
    "presentation": {
      "overflow": "scrolled",
      "continuous": true,
      "fit": "width"
    }
  },
  "readingOrder": [
    {
      "href": "./content/image1.png",
      "type": "image/png"
    },
    {
      "href": "./content/image2.png",
      "type": "image/png",
      "properties": {
        "transitionForward": {
          "type": "slide-in",
          "direction": "btt"
        },
        "transitionBackward": {
          "type": "slide-out",
          "direction": "ttb"
        }
      }
  	},
   {
     "href": "./content/image3.png",
     "type": "image/png",
     "properties": {
       "transitionForward": {
         "type": "animation",
         "sequence": [
           {
             "href": "./content/tr3-1.png",
             "type": "image/png"
            },
            {
              "href": "./content/tr3-2.png",
              "type": "image/png"
            },
            {
              "href": "./content/tr3-3.png",
              "type": "image/png"
            },
            {
              "href": "./content/tr3-4.png",
              "type": "image/png"
            },
            {
              "href": "./content/tr3-5.png",
              "type": "image/png"
            }
          ],
          "duration": 500
        }
      }
    },
    {
      "href": "./content/image4.png",
      "type": "image/png",
      "properties": {
        "transitionForward": {
          "type": "animation",
          "file": {
            "href": "./content/tr4.mp4",
            "type": "video/mp4"
          },
          "duration": 1000
        }
      }
    }
  ]
}
```