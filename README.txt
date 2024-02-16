# machine-learning-assignment-5
Max feature extraction patch and instructions for Kadenze Machine Learning class Session 5 assignment.

SUBJECTIVE STYLE CLASSIFIER FOR EXPRESSIVE DRAWING

This patch (based on a modified Max tutorial) uses expressive real-time drawing to create a type of genre or style classifier. Users decide on three “styles” of drawing, perform these in real time, and save the best examples of each style in separate banks to use for training. Users can then output these examples as x and y coordinates to Wekinator whenever they are ready, using WekiInputHelper to extract features like velocity, acceleration, standard deviation, and average position of the mouse while drawing. These features can be used to identify common expressive characteristics between the drawing styles, building a Wekinator model that can assess the similarity of future drawing performances to the original three styles.

The aim is to give users tools to identify differences in movement that would be hard to describe in words, and use these to determine suitable changes in output. I’d like to eventually use this with gesture capture and/or audio files. And with that in mind, I think this works best if one draws as if one is playing an instrument or dancing.

I felt I got the most interesting output from this system with regression rather than classification. I set up 3 outputs, each comparing 2 of the categories to each other (drawing style 1 vs drawing style 2; 1 vs 3; 2 vs 3). I built a quick Max patch adding these results together for “1ness,” “2ness” and “3ness,” and used these to control the volume of several MIDI tracks, creating a blend based on how close the current drawing input was to any of the original categories. I’ve included this output patch purely for example (went with a pretty bare-bones MIDI setup as I’m still learning, but it illustrates the idea). 

This is a Max patch, using standard objects included with the program.


INSTRUCTIONS

Recording Training Examples:

1. Decide on a "style" or "feel" you want to illustrate. I recommend basing this on a particular piece of music, or a type of expressive movement. Practice by clicking and drawing in the lcd area until you have the style or motif fairly consistent with your intent. (Hit space to clear.)

2. When you're ready, select "styleref1” on the "coll select" switch. Hit record, draw an example, and hit record again to finish. (Use r key to avoid a time lag before you start drawing.)

3. If you're satisfied with the example you recorded, click "write" under "coll actions." Give the example a name, create a folder for "style 1,” and save. If you are not satisfied with the example, simply press record and try again.

4. Record several more examples of "style 1” in the same manner, saving each under a different name in the same folder (ex: style 1a, style 1b, style 1c, etc.). Each time you press record, it will clear the current data, and record only the new points.

5. Once you feel you have enough examples of the first style, create a second style and practice it. Select "styleref2,” and follow the same process to record several examples. Save these to a second folder under a different naming scheme. 

6. Repeat for a third style, this time selecting "styleref3.”

Connecting to WekiInputHelper and Wekinator:

1. Once you have a bank of examples of each style, open WekiInputHelper. Select 2 inputs. This patch sends x and y coordinates of the drawing every 20 ms. Test by selecting "send lcd" on the "to wekinator" switch. Click and draw on the lcd. You should see OSC messages arrive.

2. Under "What to send" in WekiInputHelper, I recommend 4 features: Maximum first-order (velocity), Maximum second-order (acceleration), Standard deviation over window, and Average time over window. Ideal window size depends on how fast you are drawing. It’s not necessary to send the original values.

(Note: I used only the y value for these (input_2), as I was drawing right to left with mostly vertical variation. But x values, or both x and y, might be more useful depending on the variations between the drawing styles you choose. Regardless, I found that eliminating features which varied less from style to style made results clearer.)

3. Set up Wekinator with 4 inputs (or more if you’ve added features for x, etc.). This patch works with both classification and regression, but as mentioned above I found 3 regression outputs more interesting, each using a neural network with 3 layers. To do things this way, use outputs with a scale of 0. to 1., using the names A (style 1 vs style 2), B (style 1 vs style 3), and C (style 2 vs style 3).  

Playback of Training Examples:

1. Select “send coll” on the “to wekinator” switch. 

2. Select the bank you’d like to record from using the “coll select” switch (styleref1, etc.). Click “read” and select the example you’d like to transmit first from the pop-up folder. 

3. Click play, and hit “start recording” in Wekinator. You’ll see the recorded drawing play back on the lcd, and the x and y points sent to WekiInputHelper in real time. Click “stop recording” when done. (Use p key to make transitions quicker.) 

4. To record the next example, click read again, select the next example file, and hit play again. 

5. To use the regression model suggested above, set A and B to 0. and C to “recording off,” and record training data for style 1.; Next, Set A to 1., B to off, and C to 0. to record style 2.; Finally, Set A to off, B to 1. and C to 1. for style 3.; Train the model.

Testing the Model:

Select “send lcd” on “to wekinator” switch, click and draw on lcd screen. The patch will output x and y values to WekiInputHelper as you draw. When running the trained model, Wekinator will compare your drawing movements to your 3 “style” examples in real time, and output the results.
