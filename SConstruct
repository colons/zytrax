import os
import sys
EnsureSConsVersion(0,14);

env = Environment(CPPPATH=['#/globals','#gui','#.'],ENV=os.environ)



env.ParseConfig("pkg-config gtkmm-3.0 --libs --cflags")
env.Append(CXXFLAGS=["-g3"])

opts = Variables(ARGUMENTS)

if (os.getenv("XDG_CURRENT_DESKTOP")!=None):
	detected_platform="freedesktop"
elif (os.getenv("APPDATA")!=None):
	detected_platform = "windows"
else:
	detected_platform = ""

opts.Add(EnumVariable("platform","Platform to build",detected_platform,("windows","osx","freedesktop")))
opts.Add(BoolVariable("enable_rtaudio","Use RtAudio as Sound Driver",True))
opts.Add(BoolVariable("use_jack","Use Jack with RtAudio",False))
opts.Add(BoolVariable("use_pulseaudio","Use Pulseaudio with RtAudio",True))
opts.Add(BoolVariable("use_alsa","Use Alsa with RtAudio",True))
opts.Add(BoolVariable("enable_vst2","Enable VST2",True))
opts.Add(BoolVariable("use_wasapi","Enable Wasapi",True))
opts.Add(BoolVariable("use_directsound","Enable Wasapi",True))

opts.Update(env)  # update environment
Help(opts.GenerateHelpText(env))  # generate help

if (detected_platform==""):
	print("No build platform detected, available platforms: windows, freedesktop,osx")
	sys.exit()

if (env["enable_rtaudio"]):

	env.Append(CXXFLAGS=["-DRTAUDIO_ENABLED"])
	if (env["platform"]=="windows"):
		if (env["use_wasapi"]):
			env.Append(CXXFLAGS=["-D__WINDOWS_WASAPI__"])
		if (env["use_directsound"]):
			env.Append(CXXFLAGS=["-D__WINDOWS_DS__"])
		#env.Append(CXXFLAGS=["-D__WINDOWS_ASIO__"])
		env.Append(LIBS=["dsound","mfplat","mfuuid","wmcodecdspuuid","ksuser"])

	if (env["platform"]=="freedesktop"):

		if (env["use_pulseaudio"]):
			env.Append(CXXFLAGS=["-D__LINUX_PULSE__"])
			env.ParseConfig("pkg-config libpulse --libs --cflags")
			env.ParseConfig("pkg-config libpulse-simple --libs --cflags")
		if (env["use_alsa"]):
			env.Append(CXXFLAGS=["-D__LINUX_ALSA__"])
			env.ParseConfig("pkg-config alsa --libs --cflags")
		if (env["use_jack"]):
			env.Append(CXXFLAGS=["-D__LINUX_JACK__"])
			env.ParseConfig("pkg-config jack --libs --cflags")

if (env["platform"]=="windows"):
	env.Append(CXXFLAGS=["-DWINDOWS_ENABLED"])
	if (env["enable_vst2"]):	
		env.Append(CXXFLAGS=["-DVST2_ENABLED"])


if (env["platform"]=="freedesktop"):
	env["enable_vst2"]=False # not supported
	env.Append(CXXFLAGS=["-DFREEDESKTOP_ENABLED"])

def add_sources(self, sources, filetype, lib_env = None, shared = False):
	import glob;
	import string;
	#if not lib_objects:
	if not lib_env:
		lib_env = self
	if type(filetype) == type(""):

		dir = self.Dir('.').abspath
		list = glob.glob(dir + "/"+filetype)
		for f in list:
			sources.append( self.Object(f) )
	else:
		for f in filetype:
			sources.append(self.Object(f))
			
		
env.__class__.add_sources=add_sources


Export('env')

env.libs=[]

SConscript('globals/SCsub');
SConscript('dsp/SCsub');
SConscript('engine/SCsub');
SConscript('gui/SCsub');
SConscript('effects/SCsub');
SConscript('drivers/SCsub');
SConscript('bin/SCsub');

