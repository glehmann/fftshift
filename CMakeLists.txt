#Change PROJECT_NAME to the name of your project
PROJECT(fftshift)

# set the name of the input image used to run the test
SET(INPUT_IMAGE ${CMAKE_SOURCE_DIR}/images/fft.png)

#include some macros from another file...
INCLUDE(IJMacros.txt)

#The following lines are required to use Dart
ENABLE_TESTING()
INCLUDE(Dart)
FIND_PROGRAM(IMAGE_COMPARE ImageCompare)

#Declare any external dependencies that your project may have here.
#examples include: ITK, VTK, JPEG, PNG, OpenGL, ZLIB, Perl, Java
#If you're not sure what name to use, look in the Modules directory of your
#cmake install and check that a file named Find(Package).cmake exists
SET(Required_Packages
  ITK
)

#this foreach loads all of the packages that you specified as required.
#It shouldn't need to be modified.
FOREACH(Package ${Required_Packages})
  LOADPACKAGE(${Package})
ENDFOREACH(Package)

#Set any libraries that your project depends on.
#examples: ITKCommon, VTKRendering, etc
SET(Libraries
  ITKCommon
  ITKBasicFilters
  ITKIO
)


# install devel files
OPTION(INSTALL_DEVEL_FILES "Install C++ headers" ON)
IF(INSTALL_DEVEL_FILES)
FILE(GLOB develFiles *.h *.txx) 
FOREACH(f ${develFiles})
  INSTALL_FILES(/include/InsightToolkit/BasicFilters FILES ${f})
ENDFOREACH(f)
ENDIF(INSTALL_DEVEL_FILES)



# option for wrapping
OPTION(BUILD_WRAPPERS "Wrap library" OFF)
IF(BUILD_WRAPPERS)
   SUBDIRS(Wrapping)
ENDIF(BUILD_WRAPPERS)
   
   

#the following block of code is an example of how to build an executable in
#cmake.  Unmodified, it will add an executable called "MyExe" to the project.
#MyExe will be built using the files MyClass.h and MyClass.cxx, and it will
#be linked to all the libraries you specified above. 
#You can build more than one executable per project

IF(BUILD_TESTING)

SET(CurrentExe "check")
ADD_EXECUTABLE(${CurrentExe} ${CurrentExe}.cxx)
TARGET_LINK_LIBRARIES(${CurrentExe} ${Libraries})

ENDIF(BUILD_TESTING)

#the following line is an example of how to add a test to your project.
#Testname is the title for this particular test.  ExecutableToRun is the
#program which will be running this test.  It can either be a part of this
#project or an external executable.  After that list any args that are needed
#for this test.  Include as many tests as you like.  If your project doesn't have
#any tests you can comment out or delete the following line.
# ADD_TEST(Testname ExecutableToRun arg1 arg2 arg3)

ADD_TEST(Run check ${INPUT_IMAGE} out.png 0)
ADD_TEST(CompareImage ${IMAGE_COMPARE} out.png ${CMAKE_SOURCE_DIR}/images/test.png)
ADD_TEST(InverseRun check ${CMAKE_SOURCE_DIR}/images/test.png out2.png 1)
ADD_TEST(InverseCompareImage ${IMAGE_COMPARE} out2.png ${INPUT_IMAGE})

ADD_TEST(OddRun check ${CMAKE_SOURCE_DIR}/images/5X5.bmp shifted5X5.png 0)
ADD_TEST(OddCompareImage ${IMAGE_COMPARE} shifted5X5.png ${CMAKE_SOURCE_DIR}/images/shifted5X5.png)
ADD_TEST(InverseOddRun check ${CMAKE_SOURCE_DIR}/images/shifted5X5.png 5X5.png 1)
ADD_TEST(InverseOddCompareImage ${IMAGE_COMPARE} 5X5.png ${CMAKE_SOURCE_DIR}/images/5X5.bmp)
ADD_TEST(NonInverseOddRun check ${CMAKE_SOURCE_DIR}/images/shifted5X5.png nonInverse5X5.png 0)
ADD_TEST(NonInverseOddCompareImage ${IMAGE_COMPARE} nonInverse5X5.png ${CMAKE_SOURCE_DIR}/images/nonInverse5X5.png)
