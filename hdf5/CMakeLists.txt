set(extproject_name hdf5)
set(extproject_FILE "hdf5-1.8.15-patch1.zip")
set(extproject_URL "https://www.hdfgroup.org/ftp/HDF5/current/src/${extproject_FILE}")
set(extproject_URL_HASH "SHA1=4b4429bddb42baeb783d020c598d99605f016a03")

if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/${extproject_FILE}") 
	set(extproject_URL "${CMAKE_CURRENT_SOURCE_DIR}/${extproject_FILE}")
endif()

externalproject_add( ${extproject_name} 
					URL ${extproject_URL}							
					URL_HASH ${extproject_URL_HASH}							
					CMAKE_ARGS 
					-DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}/${extproject_name}
					-DBUILD_SHARED_LIBS=OFF
					-DBUILD_TESTING=OFF
					-HDF5_BUILD_EXAMPLES=OFF
					-HDF5_BUILD_TOOLS=OFF
					)