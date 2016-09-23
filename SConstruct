import os
env = Environment(ENV = os.environ)

# enable choosing other compilers
env["CC"] = os.getenv("CC") or env["CC"]
env["CXX"] = os.getenv("CXX") or env["CXX"]
env["ENV"].update(x for x in os.environ.items() if x[0].startswith("CCC_"))

env["LIBVER"] = "0.9.0"

env["PREFIX_PATH"] = ARGUMENTS.get('prefix', "/usr/local")
env["INCLUDE_PATH"] = ARGUMENTS.get('include', env['PREFIX_PATH'] + "/include")
env["LIB_PATH"] = ARGUMENTS.get('lib', env['PREFIX_PATH'] + "/lib")

def CheckPKGConfig(context, version):
	context.Message( 'Checking for pkg-config... ' )
	ret = context.TryAction('pkg-config --atleast-pkgconfig-version=%s' % version)[0]
	context.Result( ret )
	return ret
def CheckPKG(context, name):
	context.Message( 'Checking for %s... ' % name )
	ret = context.TryAction('pkg-config --exists \'%s\'' % name)[0]
	context.Result( ret )
	return ret

conf = Configure(env, custom_tests = { 'CheckPKGConfig' : CheckPKGConfig,
	'CheckPKG' : CheckPKG })

if not conf.CheckPKGConfig('0.15.0'):
	print 'pkg-config >= 0.15.0 not found.'
	Exit(1)

env['HAVE_OPENGL'] = conf.CheckPKG('gl')
env['HAVE_SDL2'] = conf.CheckPKG('sdl2')

if env['HAVE_SDL2']:
	if not conf.CheckPKG('SDL2_image'):
		print 'SDL2_image not found. Disabling SDL2 support.'
		env['HAVE_SDL2'] = 0
	if not conf.CheckPKG('SDL2_ttf'):
		print 'SDL2_ttf not found. Disabling SDL2 support.'
		env['HAVE_SDL2'] = 0

env = conf.Finish()

env.Append(CPPPATH = ['#include/'])
env.Append(LIBPATH = ['#src/'])
env.Append(CFLAGS = ['-g'])
env.Append(CPPFLAGS = ['-g'])

Export("env")

# Main static library
SConscript('src/SConscript')

# Example code
SConscript('examples/SConscript')

# TODO: install
common_headers = []
widgets_headers = []
sdl2_headers = [
	'include/guisan/sdl/sdlgraphics.hpp',
	'include/guisan/sdl/sdlimage.hpp',
	'include/guisan/sdl/sdlimageloader.hpp',
	'include/guisan/sdl/sdlinput.hpp',
	'include/guisan/sdl/sdlpixel.hpp',
	'include/guisan/sdl/sdltruetypefont.hpp'
]
opengl_headers = []

env.Install(env["LIB_PATH"], 'src/libguisan.a')
env.Install(env["LIB_PATH"] + "/pkgconfig", 'src/guisan.pc')
env.Install(env["INCLUDE_PATH"], 'include/guisan.hpp')
# install common headers + widgets

if env['HAVE_SDL2']:
	env.Install(env["INCLUDE_PATH"] + '/guisan/sdl', sdl2_headers)
if env['HAVE_OPENGL']:
	#install gl headers
env.Alias('install', env["PREFIX_PATH"])
