aux_source_directory(. src_list)

foreach(src ${src_list})
    get_filename_component(basename ${src} NAME_WE)
    add_library(${basename} SHARED ${src})
    target_link_libraries(${basename} PRIVATE node-addon-api)
    target_include_directories(${basename} PRIVATE common)
endforeach()