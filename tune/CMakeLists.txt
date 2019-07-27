add_executable(tuneup tuneup.c)
target_link_libraries(tuneup libmpfr)

add_library(speed speed.c)
target_link_libraries(speed libmpfr)

add_executable(bidimensional_sample bidimensional_sample.c)
target_link_libraries(bidimensional_sample libmpfr)
