include_directories (${CheMPS2_SOURCE_DIR}/CheMPS2/include/chemps2/ ${HDF5_INCLUDE_DIRS})

set (CHEMPS2LIB_SOURCE_FILES "CASPT2.cpp"
                             "CASSCF.cpp"
                             "CASSCFdebug.cpp"
                             "CASSCFnewtonraphson.cpp"
                             "CASSCFpt2.cpp"
                             "ConjugateGradient.cpp"
                             "ConvergenceScheme.cpp"
                             "Correlations.cpp"
                             "Cumulant.cpp"
                             "Davidson.cpp"
                             "DIIS.cpp"
                             "DMRG.cpp"
                             "DMRGfock.cpp"
                             "DMRGmpsio.cpp"
                             "DMRGoperators.cpp"
                             "DMRGoperators3RDM.cpp"
                             "DMRGSCFindices.cpp"
                             "DMRGSCFintegrals.cpp"
                             "DMRGSCFmatrix.cpp"
                             "DMRGSCFoptions.cpp"
                             "DMRGSCFrotations.cpp"
                             "DMRGSCFunitary.cpp"
                             "DMRGSCFwtilde.cpp"
                             "DMRGtechnics.cpp"
                             "EdmistonRuedenberg.cpp"
                             "Excitation.cpp"
                             "FCI.cpp"
                             "FourIndex.cpp"
                             "Hamiltonian.cpp"
                             "Heff.cpp"
                             "HeffDiagonal.cpp"
                             "HeffDiagrams1.cpp"
                             "HeffDiagrams2.cpp"
                             "HeffDiagrams3.cpp"
                             "HeffDiagrams4.cpp"
                             "HeffDiagrams5.cpp"
                             "Initialize.cpp"
                             "Irreps.cpp"
                             "Molden.cpp"
                             "PrintLicense.cpp"
                             "Problem.cpp"
                             "Sobject.cpp"
                             "SyBookkeeper.cpp"
                             "Tensor3RDM.cpp"
                             "TensorF0.cpp"
                             "TensorF1.cpp"
                             "TensorGYZ.cpp"
                             "TensorKM.cpp"
                             "TensorL.cpp"
                             "TensorO.cpp"
                             "TensorOperator.cpp"
                             "TensorQ.cpp"
                             "TensorS0.cpp"
                             "TensorS1.cpp"
                             "TensorT.cpp"
                             "TensorX.cpp"
                             "ThreeDM.cpp"
                             "TwoDM.cpp"
                             "TwoIndex.cpp"
                             "Wigner.cpp")

add_library (chemps2-base OBJECT ${CHEMPS2LIB_SOURCE_FILES})

if (NOT STATIC_ONLY)
    set_target_properties (chemps2-base PROPERTIES POSITION_INDEPENDENT_CODE 1)
    add_library (chemps2-shared SHARED $<TARGET_OBJECTS:chemps2-base>)
    target_link_libraries (chemps2-shared ${LAPACK_LIBRARIES} ${HDF5_LIBRARIES})
    set_target_properties (chemps2-shared PROPERTIES SOVERSION ${CheMPS2_LIB_SOVERSION} CLEAN_DIRECT_OUTPUT 1 OUTPUT_NAME "chemps2")
endif (NOT STATIC_ONLY)

add_library (chemps2-static STATIC $<TARGET_OBJECTS:chemps2-base>)
target_link_libraries (chemps2-static ${LAPACK_LIBRARIES} ${HDF5_LIBRARIES})
set_target_properties (chemps2-static PROPERTIES CLEAN_DIRECT_OUTPUT 1 OUTPUT_NAME "chemps2")

if (NOT STATIC_ONLY)
    install (TARGETS chemps2-shared DESTINATION ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR})
endif (NOT STATIC_ONLY)
install (TARGETS chemps2-static DESTINATION ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR})
install (DIRECTORY include/chemps2/ DESTINATION include/chemps2 FILES_MATCHING PATTERN "*.h")

link_directories (${CMAKE_BINARY_DIR}/CheMPS2)
add_executable (chemps2bin executable.cpp)
if (STATIC_ONLY)
    add_dependencies (chemps2bin chemps2-static)
    target_link_libraries (chemps2bin chemps2 ${LAPACK_LIBRARIES} ${HDF5_LIBRARIES})
else (STATIC_ONLY)
    add_dependencies (chemps2bin chemps2-shared)
    target_link_libraries (chemps2bin chemps2)
endif (STATIC_ONLY)
set_target_properties (chemps2bin PROPERTIES OUTPUT_NAME "chemps2")

install (TARGETS chemps2bin DESTINATION ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_BINDIR})

