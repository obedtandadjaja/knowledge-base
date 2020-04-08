# Non-Maximum Suppression (NMS)

NMS is most commonly used in object detection pipeline which generates proposals for classification. Proposals are nothing but the candidate regions for the object of interest.

Other approaches employ a sliding window over the feature map and assigns foreground/background scores depending on the features computed in that window. This scoring is applied to all of the candidate regions, resulting in hundreds of proprosals. This calls for a technique to filter the proposals based on some criteria.

## Input

- List of proposal boxes B
- List of confidence scores S
- List of overlap threshold N

## Output

List of filtered proposals D

## Rough algorithm

1. Select the proposal with highest confidence score, remove it from B and add it to the final proposal list D
1. Compare this proposal with all the proposals - calculate the IOU (Intersection over Union) of this proposal with every other proposal. If the IOU is greater than the threshold N, remove the proposal from B
1. Repeat step 1 and 2 from the remaining proposals in B until there are no more proposals left in B

Sidenote: IOU calculates the amount of area that is in the intersection and compares that to the amount of area that is in the union.

For this reason, the selection of threshold value is key for the performance of the mode. However, setting this threshold is tricky and needs some kind of context to the problem.

## Potential problems

Picture an image with three horses overlapping each other. An object detection that specializes on detecting horses will create 3 overlapping candidate regions with high confidence levels. Using the algorithm above, and say we use a threshold of 0.5, the candidate with the highest confidence level will be the only resulting candidate regions since the other candidate regions are deleted since they fall under the 0.5 IOU threshold.

To account for this issue, instead of completely removing the proposals with high IOU and high confidence, reduce the confidences of the proposals proportional to IOU value. As a result, the candidate regions will not be removed, and instead their confidence levels will be altered a little bit.
