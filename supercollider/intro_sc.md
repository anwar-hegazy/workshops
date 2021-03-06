# Supercollider
*Spring 2015*

Note: anything in between carrots (`<` to `>`) denotes user input or user key commands.

*Quick reference links*

[Supercollider](https://supercollider.github.io/), this is the main SC page these days.

[Docs](http://doc.sccode.org/), the docs will be installed on your machine with the application, but they can be accessed here as well.

[The SC Book](http://www.supercolliderbook.net/), the best book on SC (this website contains all of the code from the book, btw).

[SCCode](http://sccode.org/), a web-platform for sharing and remixing supercollider code.

[SCTweets](http://sctweets.tumblr.com/), supercollider code in 140 characters or less (frequently amazing).

[SC Mailing List](http://www.birmingham.ac.uk/facilities/ea-studios/research/supercollider/mailinglist.aspx), this is the main place to go for support other than the docs. All of the devs are very active on this list and respond quickly to questions.

## Download
Go [here](https://supercollider.github.io/download.html) to download SuperCollider. Once your download is complete, go ahead and install.

## About
Supercollider is made up of two applications: a language interpreter and one or more synthesis servers. All communication between the `lang` (short for language) and the `server` is done via [OpenSoundControl](http://opensoundcontrol.org/).

## Starting the server
In order to make sound the server must be running. Type the following into a new window (`<Command+N>` makes a new window) and then hit `<Shift + Enter>` on the same line to execute the code:

```supercollider
s.boot;
```

Here `s` refers to the `localhost` server on your CPU.

`s.boot;` generally results in something like this in the post window:

```supercollider
Welcome to SuperCollider 3.6.6. For help type cmd-d.
localhost
Found 0 LADSPA plugins
Number of Devices: 16
   0 : "AirPlay"
   1 : "Built-in Microph"
   2 : "Built-in Output"
   3 : "After Effects 10.5"
   4 : "After Effects 11.0"
   5 : "After Effects 11.0.1"
   6 : "After Effects 11.0.2"
   7 : "After Effects 12.0"
   8 : "After Effects 12.2"
   9 : "After Effects 13.0"
   10 : "Prelude 1.0"
   11 : "Prelude 2.0"
   12 : "Premiere Pro 5.5"
   13 : "Premiere Pro 6.0"
   14 : "Premiere Pro 7.0"
   15 : "Premiere Pro 8.0"

"Built-in Microph" Input Device
   Streams: 1
      0  channels 2

"Built-in Output" Output Device
   Streams: 1
      0  channels 2

SC_AudioDriver: sample rate = 44100.000000, driver's block size = 512
SuperCollider 3 server ready.
Receiving notification messages from server localhost
Shared memory server interface initialized
```

One can quit the server by executing the following code:

```supercollider
s.quit;
```

## Functions
Similar to most other programming language, Functions in SC are denoted by the use of curly brackets. Anything between `{ }` is a function.

*For Example*

```supercollider
f = { "hello world!".postln; };
f.value;
```

The first line of code stores the function at `f`, whereas the second line returns the `value` (in this case, prints the message "hellow world!" to the post window) associated with the function.

## Arguments and Variables within Functions
Arguments allow one to pass values to a function when the function is called.

*For Example*

```supercollider
(
f = { arg a, b;
    a - b;
};
f.value(5, 3);
)
```

In the above code, evaluating the `f.value(5, 3)` results in the number `2` at the post.

And, of course, one can also use variables in functions.

*For Example*

```supercollider
(
f = { arg a, b;
    var firstResult, finalResult;
    firstResult = a + b;
    finalResult = firstResult * 2;
    finalResult;
};
f.value(2, 3);    // this will return (2 + 3) * 2 = 10
)
```

## Making Sound
Functions are used to make sound in SC. Execute the line below and, when you want it to stop, just type `<Command + .>`.

```supercollider
{ SinOsc.ar(440, 0, 0.2) }.play;
```

The code above plays a [Sine Wave](https://en.wikipedia.org/wiki/Sine_wave) with a frequency of 440Hz and an amplitude of 0.2 (amplitude, or what one can think of as volume, is generally kept within the range 0.0 to 1.0 in SC).

SC allows one to plot sounds like this onto a graph with `.plot`. This is useful if your sound is less deterministic than the above or if you need to check why something you are trying to do is inaudible or whatever.

*For Example*
```supercollider
{ SinOsc.ar(440, 0, 0.2) }.plot;
```

Which will result in this:
![test](sineshot1.png)

SC also has an oscilloscope method (`.scope`) which will display the changing waveform while playing it:

*For Example*

```supercollider
{ SinOsc.ar(440, 0, 0.2);
    LFTri.ar( 440, 0, 0.2 ); }.scope;
```

Which will result in this:
![test](sineshot2.png)

While `.plot` plots the function over time, and `.scope` shows a real-time plot of the waveform, both of those methods are generally used for testing and not for performance or recording.

## .play

`.play` simply tells the server to start a process. The result of that process depends on the function.

*For Example*

```supercollider
{ SinOsc.ar(440, 0, 0.2) }.play;
```

So, `{ ... }` returns a function (which can be killed with `.stop`), whereas `{ ... }.play` returns a `Synth` object. `Synth` objects do not have a `.stop` method, so one must either `.free` or `.release` them in order to stop the sound.

*For Example*

```supercollider
x = { SinOsc.ar(440, 0, 0.2) }.play;
//lets wait a moment
x.release;
```
Interestingly enough, one can call `.scope` on the entire output of the server. Think of this like putting everything that is currently happening in SC through an oscilloscope.

*For Example*

```supercollider
{ [SinOsc.ar(440, 0, 0.4), LFTri.ar(220, 0.0, 0.2) ] }.play;
s.scope;
```
results in:

![test](shot3.png)

## Ugens

`UGens` can be used to output audio (with the `.ar`, or audio rate, class method) or control information (with the `.kr`, or control rate, class method).

*For Example*

```supercollider
(
{ var ampOsc;
    ampOsc = SinOsc.kr(0.5, 1.5pi, 0.5, 0.5);
    SinOsc.ar(440, 0, ampOsc);
}.play;
)
```

In the above example, the `SinOsc` `UGen` is used both as control rate (to change the amplitude of the sounding `SinOsc`) and as audio rate (to actually play the `Sine` tone).

Another example that uses Mouse location (both x and y axes) for frequency and amplitude:

```supercollider
{ SinOsc.ar(MouseY.kr( 50, 2000), 0.0, MouseX.kr( 0.0,1.0 )); }.scope;
```

## Additive Synthesis

Additive Synthesis is a general term that refers to the technique of playing more than one sound simultaneously to create a more complex sound. This is also typically referred to as "summing" signals.

*For Example*

```supercollider
{ SinOsc.ar( 440, 0.0, 0.5 ) + PinkNoise.ar( 0.1 ) + Crackle.ar( 1.5, 0.4 ) + LFTri.ar( 200, 0.0, 0.2 )}.play;
```

BTW, Crackle and Dust are my absolute favorite noise UGens/Random number generators. Be careful with Crackle, it explodes easily (and this especially sucks if you are monitoring SC via headphones).

## SynthDefs

SC has an optimized way of taking in information about `UGens` and their interconnections: `SynthDef`s. A `SynthDef` tells the server how to generate audio and translates that information to bite code. You can think of a `SynthDef` and its resulting `Synth` in a similar manner that one thinks about classes and objects: a `SynthDef` is the skeleton that defines a particular  instance of a playing `Synth`.

There are two basic design patterns for SynthDef's. Choosing one over the other depends on what you need:

### mute/unmute neverending synth

```supercollider
SynthDef( \sin,	{ | amp = 0.0, freq = 440, trig = 0 |
	var env, sig;
	env = EnvGen.kr( Env.asr( 0.001, 0.9, 0.001 ), trig, doneAction: 0 );
	sig = SinOsc.ar( [ freq, freq * 0.999 ], 0.0, amp ) * env;
	Out.ar( [ 0, 1 ], sig * 0.6 );
}).add;

x = Synth( \sin, [ \freq, 400, \amp, 0.9]);     //this plays a \sin object and stores it at x

x.set(\trig, 1);        //uses the .set class to change the \trig argument to 1, which unmutes our running synth
x.set(\trig, 0 );       //.set mutes the synth by setting \trig back to 0

s.scope;                    //send server output through the scope
```

### create a new synth instance on trig

A longer example (which also demonstrates OSC controller stuff, private fx busses, and sample playback):

```supercollider
~bSamp = Buffer.read( s, "/Users/mdp/Desktop/salton_sea_test_tracks_01_12_14.wav" );
~len = ((~bSamp.numFrames)/(~bSamp.sampleRate));

(
SynthDef( \play, { | amp = 0.9, dur = 1, trig = 1, bufnum, rate = 1, effectsBus |
	var env, line, play, sig;

	env = EnvGen.kr( Env.asr( 0.01, amp, 0.01 ), trig, doneAction: 2 );        //notice that doneAction is set to 2 here
	line = Line.kr( 0.0, 1.5, dur, 0.9, doneAction: 0 );
	play = PlayBuf.ar( 1, bufnum, line, loop: 1 ) * env;
	Out.ar( effectsBus, play );        //so this sample is being played, but not monitored (i.e. not audible) on a bus
}).add;

SynthDef( \bpf_delay, { | inBus, bD = 0.0, maxd = 2, delaytime = 0.0, freq = 0.0, trig = 0, rq = 0.5, sF, dur, amp = 0.0 |
	var delay, env, in, line;

	in = In.ar( inBus, 2 );                    //here i establish a way to read input from elsewhere into this Synth
	line = Line.kr( Rand(sF, sF * 12), Rand(sF, sF * 12), dur );           //i use lots of random number generators on prototyping stuff, here is an example of one way of doing this
	delay = BPF.ar( DelayN.ar( in, maxd, Rand(0.01, maxd ) ), line, rq, amp);
	env = EnvGen.kr( Env.perc( 0.01, dur, amp ), trig, doneAction: 2 );
	Out.ar( [ 0, 1 ], env * delay);
}).add;

)

b = Bus.audio(s, 2 );                   //make an audio bus
~vol = Bus.control(s, 1).set(0.99);     //make and set a control bus (ditto next two lines)
~dur = Bus.control(s, 1).set(20.0);
~rq  = Bus.control(s, 1).set(0.3);

~y = Synth(\play, [\amp, 0.99, \trig, 1, \dur, ~len, \bufnum, ~bSamp, \effectsBus, b.index ]);

// OSC
OSCFunc({|msg, time, addr, recvPort|

	if( msg[1] == 1,{
		Synth.after(~y, \bpf_delay, [\inBus, b.index, \sF, 50, \trig, 1, \dur, ~dur.asMap, \rq, ~rq.asMap, \amp, ~vol.asMap ] );
	});
}, '/1/push1');

OSCFunc({|msg, time, addr, recvPort|
//	msg[1].postln;
	~vol.set(msg[1] );
}, '/1/fader1');

OSCFunc({|msg, time, addr, recvPort|
	var len;
	len = msg[1].linlin( 0.0, 1.0, 5, 40 );
//	len.postln;
	~dur.set( len );
}, '/1/fader2');

OSCFunc({|msg, time, addr, recvPort|
	var len;
	len = msg[1].linlin( 0.0, 1.0, 0.1, 0.01 );
//	len.postln;
	~rq.set( len );
}, '/1/fader3');

s.scope;
```
