#############################################################################

if( WIN32 )

include_directories( msvc )

file( GLOB LIB_USB_FILES 	msvc/config.h 
							core.c 
							descriptor.c 
							hotplug.c 
							hotplug.h 
							io.c 
							libusb.h 
							libusb-1.0.rc 
							libusbi.h 
							os/poll_windows.c 
							os/poll_windows.h 
							strerror.c 
							sync.c 
							os/threads_windows.c 
							os/threads_windows.h 
							version.h 
							version_nano.h 
							os/windows_usb.c 
							os/windows_usb.h
							libusb.def )
							
	# this settings have been taken from the original libusb solution project
	# disable warning	
	add_definitions(-D_CRT_SECURE_NO_WARNINGS)	
	# Set warning level
	add_definitions(/W4)

	 
elseif( APPLE )

include_directories( xcode )
	
file( GLOB LIB_USB_FILES 	xcode/config.h
				version_nano.h
				version.h
				sync.c
				strerror.c
				os/threads_posix.c 
				os/threads_posix.h
				os/poll_posix.c 
				os/poll_posix.h 
				os/darwin_usb.c 
				os/darwin_usb.h
				libusb.h
				libusbi.h
				io.c
				hotplug.c 
				hotplug.h
				core.c 
				descriptor.c )



endif( WIN32 )

#Add libusb library
add_library( ${LIB_USB} SHARED ${LIB_USB_FILES} )

if( APPLE )
	#Remove prefix because Xcode add "lib" before library name
	set_property(TARGET ${LIB_USB} PROPERTY PREFIX "")
	find_library(IOKIT_FRAMEWORK IOKit)
	find_library(COREFOUNDATION_FRAMEWORK CoreFoundation)
	target_link_libraries(${LIB_USB} ${COREFOUNDATION_FRAMEWORK} ${IOKIT_FRAMEWORK} libobjc.dylib )
	set_target_properties( ${LIB_USB} PROPERTIES XCODE_ATTRIBUTE_INSTALL_PATH @executable_path)	
endif( APPLE )

#############################################################################
# Winsocket 
if( WIN32 )
	add_definitions(-D_WIN32_WINNT=0x0601)
	#Change debug iterator level in according to the default qt library
	set(CMAKE_CXX_FLAGS_RELWITHDEBINFO "-D_ITERATOR_DEBUG_LEVEL=0")
endif( WIN32 )