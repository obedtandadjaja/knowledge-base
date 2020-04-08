# Text Detection

Natural scene text detection is very challenging. Detecting text in constrained, controlled environments is so much easier and can be accomplished using heuristic-based approaches (i.e. exploiting gradient information or the fact that text are typically grouped together).

Some of the challenges to do with natural scene text detection are:

- Image/sensor noise. Sensor noise from a handheld camera is typically higher than that of a traditional scanner.
- Viewing angles. The scene might not have a viewing angle that is parallel to the text.
- Blurring.
- Lighting condition. We cannot control if the camera has flash on, or if the sun is shining brightly.
- Resolution. Differences in camera quality might affect the amount of noise in the scene.
- Non-paper objects. Paper is not reflective, but text that appear on a reflective object makes it harder to detect.
- Unknown layout. Cannot have priori information to give our algorithms "clues" as to where the text resides.

## Heuristic-based text detection

We can use basic image processing techniques such as thresholding, morphological operations, and contour properties. We can apply these techniques to our assumptions on common properties of text:

- Text is typically dark, so we can probably just turn the image into grayscale
- Text typically do not have gradient so we can do some thresholding to turn the pixels to binary (black or white)
- Text tend to be in an area together so we can apply some morphing strategies to get a rough area of where the text is

## Natural scene text detection

### EAST deep learning text detector

East stands for An Efficient and Accurate Scene Text Detector (reference: https://arxiv.org/abs/1704.03155). It is a pipeline capable of predicting words and lines of text at arbitrary orientations on 720p images, and can run at 13 FPS.

Most importantly, since the pipeline is end-to-end, it is possible to sidestep cmputationally expensive sub-algorithms that other text editors typically apply. For more information about the architecture design and training methods, read the reference in the previous paragraph.

### Text Parsing with Tesseract

Tesseract is a highly accurate deep leraning-based model for text recognition. Whereas EAST is only detecting where the text is, Tesseract is commonly used as a follow-up to parse the text in the area previously detected by EAST. Tesseract performs quite poorly if there is a significant amount of noise or the image is not properly preprocessed and cleaned before applying Tesseract. 

Tesseract was first created by HP, but was picked up by Google in 2006 and is maintained by the company ever since.
