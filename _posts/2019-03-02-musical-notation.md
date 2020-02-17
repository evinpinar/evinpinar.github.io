---
layout: post
title: Towards a New Musical Notation
categories: [music, AI]
comments: true
---

Think about a cello performance, of Yo-Yo-Ma playing Bach Suite 3, Gigue. Now think of Rostropovich. They actually play very differently which can be understood by an average classical music listener.

Now, check the music sheets of Bach’s pieces. There are nothing other than some black dots, which represent some notes on an F key. They are usually written in a very simple form: no bow indications nor accents. There are no indications of how to play each of these tones.

Since there are no indications, the interpretation of each performer has an important role on the style of the piece. Yo-Yo-Ma bounds notes together, whereas Rostropovich puts soft staccatos on these. We understand it either by listening their recordings and/or by watching.

After a long time of musical evolution, the number of “style indicators” has increased. During the classic period, styles of the pieces and how they should be played became more restricted. At romantic period, musical terminology grew up and many many Italian/French/German words that indicate the emitted emotions increased. Currently, how a piece should be played mainly depends on composer’s decision. Some give more freedom of interpretations or some define all possible details.

What are the possible details then? Can we specify every imaginable musical detail and put them into the music sheet? In some sense, number of words and detail increase when it is found to be necessary. There is a long process of writing, testing, writing and testing. The musical notation and theory has evolved in a similar way. Though, it is not that simple, because the aim to compose a classical piece is to let others play it. A set of possible limits are given by the composer, and all other points that are not predefined are in the hands of the performer. The performer adds her own emotions and delivers to the listeners. They sometimes record a piece, but then it becomes a piece played by a specific person, and it should not be really compared to others. Now it is a new unique piece.

Following these questions, I believe that artificial intelligence can play a significant role on music development. When I say this, please don't imagine AI composing new pieces. Instead, I imagine intelligent systems which can capture musicians better than humans. For example, they can distinguish the visual differences, generalize the patterns and compare large amount of musicians to each other which is not very possible for a human without many years of experience. Likewise, they can hear the subtle differences in sounds, process signals and figure out the distributions of pieces, composers and musicians. Such systems will be able to handle large amounts of data in a fast manner. My main questions arose from a theoretical view, however they also have practical effects, such as in music education, creative music generation, and performance arts. 

I will be collecting my ideas here. If I can get some resources and freetime, I will start working on this project. So this is a "project proposal blogpost" which will be iterated and continued. 

What I aim is to:
1. Watch videos of different musicians that play the same piece, say Bach’s Gigue
2. Detect the differences in performer’s styles visually
3. Find out possible extensions to the existing music notation, being it either syntactic or semantic
4. Propose/generate a new sound according to the possible learned points. There might be two options here: 1. mix the styles of different sentences from different players. 2. Really generate a new perspective.(This might indicate that an AI can have true **creativity**.)

This will be helpful in generating the better versions of currently played music. For instance, Rachmaninoff’s piano concerto’s are played by many, and there are recordings from the composer himself. Those recorded musicians probably initially listen to the original recordings, and they try to arise similar emotions when they play. Though, it might be helpful for them if there would be additional cues and tips along the notation.

------
Update on Jan 20, 2020

How do we perceive a musician who is performing on the stage during a concert? These are the main components: 
1. Hearing the music: We should be able to infer the sounds in an online manner, ie. immediately act upon, understand the pitch, tempo, loudness, emotional characteristics. These aspects should be associatable with the preheard music. The differences with the same songs listened before should be easily pointed. It should realize fine details: is the pitch correct, how loud is that tone, as well as the longer term details: tempo, emotions, sentences and paragraphs. In this aspect, the problem is very similar to language understanding. So I think the distributional modeling plays an important role. But these distributions can be altered and modified. This can be done by attaching or using the musical "attributes". Moreover, the emotion indicating words might also have a distributional manifold which can be mapped on the musical manifold.
In a very simple manner, music distribution can be seen as a long image. This long image can be cut in sentences, bars and notes. In language, the hierarchy is paragraph -> sentence -> word -> character. However, music is represented with a higher dimension so it requires more sophisticated models than the ones used language. 

2. Watching the player: For example when watching a cellist, we see how the arms, head, cello, and performer's body move. Each of the joints have a very specific role while playing. They move in a balance: upper joints and muscles release while wrist opens up. There are similar movements or patterns while doing different actions other than playing. For example while swimming, cleaning a table :) Such visual differences are hard for people to realize unless they really care attention and watch them over and over. This requires a perfect temporal 3D understanding of the human body. Joints, arms, human pose should be perceived, used to create a hypothetical model which defines the movement of these parts in relation to each other. Such perceived features should be generalizable: similar performers should be cumulated, the differences be pointed out, necessary changes to play in a particular style should shown. Here, I imagine an automata like, self-updating graph that represent the joints. Edges might represent how muscles in between the joints contract. It should show the temporal changes, maybe through updating the automata machine.  

3. Connecting the previous parts together: Which motion creates which sound? How to move in order to create a specific sound? How to correct the sound by modifying the motion? Are there general patterns in motion-sounds? What are differences in particular samples-> do they move differently and create same sound, or vice versa, move similar but create different sounds?

All of these points create different research directions and have many interesting possibilities and things to try. I guess it is not possible to accomplish this task as a single person, because one can dedicate her whole life even only to narrower issues: signal and music processing or vision understanding... It seems that this project requires several talented researchers, engineers and financial funding. But writing does not require anything. Also, I might start from any question and see how it works.

Some simple things to do:

- Collect clearly seen videos of different cello players that play cello suits. Label the data: segmentation of instrument, hand, arm, upper torso parts, poses. 
- Read through the music sheets, detect notes, keywords etc... 
- Get the video sounds, make sure they are aligned with frames. 
- Model the sound signals, video, and written notes-keywords together (VideoBert, Text-To-Speech, Sound spatial localization on videos, temporal video localization...). One should be able to retrieve the others. (this is not a simple task but it can be built by inspiring the existing multimodal models).  


_to be continued..._
