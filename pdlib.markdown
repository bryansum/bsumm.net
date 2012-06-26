---
layout: default
title: pdlib
---

# pdlib

*An open source port of Pure Data to the iPhone.*

### [github.com/bryansum/pdlib](http://github.com/bryansum/pdlib)

The need for pdlib came from the development of [iJam](/ijam.html), which needed an audio back end. After looking for existing solutions, few synthesizers were free to use in commercial projects, and those that available were typically limited in the sounds they could produce. I was familiar with [Rjdj](http://rjdj.me) and their decision to use [Pure Data](http://crca.ucsd.edu/~msp/software.html) as their audio synthesizer, but they hadn't opted to publicly release their port.

One of the goals of pdlib was to minimize changes to vanilla Pure Data; as such, only the command-line parsing and the audio hardware interface have been rewritten. It is based on Pure Data v0.42-5.

The <a href="http://github.com/bryansum/pdlib">project is currently hosted on Github</a> under
the FreeBSD license for my contribution. Pure Data itself is under a BSD-style license.
Be aware, though, that `PdListTest` uses example code from Martin Peach for the Pure Data OSC
parts and his work is under GPL.

<p>As it stands, Pure Data wasn't really written to be embedded in other systems,
and was dependent on <a href="http://www.portaudio.com/">PortAudio</a>, which wasn't
compatible with the iPhone's audio system. I ended up basically rewriting the audio
hardware interface layer of Pure Data to use the iPhone's RemoteIO Audio unit.
Apple's documentation for that system is sparse, so I found articles by
<a href="http://atastypixel.com/blog/2008/11/04/using-remoteio-audio-unit/">Mike Tyson</a>
and <a href="http://www.subfurther.com/blog/?p=507">Chris Adamson</a> to be invaluable.</p>

<p>The current iteration of pdlib doesn't explicitly support any communication between
the host programming environment and the Pure Data system. What I've done for
iJam is use Martin Peach's <code>osc</code> patches, specifically <code>routeosc</code>, along with his
<code>udpreceive</code> patch to receive audio events from the Objective-C code on a specific port.
Then we use <a href="http://liblo.sourceforge.net/">liblo</a> to send OSC messages so
communication is all socket-based. This was done in part because Pure Data is
<em>not</em> thread-safe. Because only one instance of PD can run in a single process,
the PdController library runs as a singleton. PdLibTest, included
in the release, shows how pdlib can be used in an Xcode project.</p>

<h2>Installation</h2>

<p>To build and install pdlib as a static library for linking into other iPhone
projects, I've created a shell script which kicks off the requisite Xcode builds
and creates a fat binary. This has been tested on OS X 10.6, but should work on 10.5
as well. This takes in a prefix argument. In short, to install
pdlib to <code>$YOUR_PREFIX</code>:</p>

<pre><code>cd PdLib
./install.sh $YOUR_PREFIX
</code></pre>

<p>Then make sure to add both <code>$YOUR_PREFIX/include</code> to your project's Header Search
Path as well as <code>$YOUR_PREFIX/lib</code> to the Library Search Path in addition to adding
$YOUR_PREFIX/lib/libpd.a to your current target's linked libraries.</p>

<p>PdLibTest is dependent on liblo, which is included in the package. As building
fat static libraries for iPhoneOS is somewhat complicated, I made a quick install
script that installs a fat binary of liblo for iPhone into <code>/usr/local</code>. This uses
a <code>build_for_iphone</code> script written by <a href="http://pseudogreen.org/">Christopher Stawarz</a>.
PdLibTest is already preconfigured to search for the <code>/usr/local</code> header and library paths
(<code>/include</code> and <code>/lib</code>, respectively). To install liblo for iPhone:</p>

<pre><code>cd liblo-0.26
sudo ./quickinstall.sh 
</code></pre>

<h2>Usage</h2>

<p><code>PdController</code> is an Objective-C singleton, and has a number of properties that
can be set to change how Pure Data is configured. Note that changes in these
properties or only reflected in Pure Data after a start or restart of the system.
The properties include:</p>

<ul>
<li><code>soundRate</code>: the frequency of Pure Data in Hz. Defaults to 22050</li>
<li><code>blockSize</code>: How many samples to generate for each DSP tick. Smaller blocks sizes
mean smaller latency, but is more processor intensive. I've defaulted to 256,
while vanilla Pure Data is set to 64. Because of this, this value needs to be
a multiple of 64.</li>
<li><code>nOutChannels</code>: 1 or 2. How many channels we want to support. Set to 2 for now.
Changing to mono has not been tested, but <em>should</em> work.</li>
<li><code>callbackFn</code>: Optional; a function that is to be called on every DSP tick. This is
often useful as a timer mechanism for UI events, etc. The callback function
should run quickly, as this runs on CoreAudio's callback thread and needs to finish
before the next DSP cycle. In other words, try to minimize system calls, etc.
I typically use it to call GUI events asynchronously, etc. with
<code>performSelectorOnMainThread:</code>.</li>
<li><code>externs</code>: an <code>NSArray</code> of <code>NSString</code>s. Each should be a valid path to externs
that should be loaded into the system at start-up.</li>
<li><code>openfiles</code>: an <code>NSArray</code> of <code>NSString</code>s that specify which files should be opened
as PD starts up.</li>
<li><code>liddir</code>: an <code>NSString</code> that specifies the directory where the .pd files are located.
This is recursive.</li>
</ul>


<p>See PdListTest for a working example of PdController.</p>

<h2>Future improvements</h2>

<ul>
<li>Add a mechanism to directly send messages to Pd via PdController</li>
<li>Add support for microphone input (ADC~)</li>
<li>Add test cases</li>
<li>Verify that <code>openFile:</code> is working correctly</li>
</ul>
