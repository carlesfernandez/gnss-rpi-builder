cmake_minimum_required(VERSION 2.8)
project(gnss-rpi-builder NONE)

###############################################################################
# Find required program
###############################################################################
function(required_program var names doc)
  find_program(${var} NAMES ${names} DOC ${doc})
  if (NOT ${var})
    message(FATAL_ERROR "Program not found (${names})")
  else()
    message(STATUS "Using " ${${var}})
  endif()
endfunction()

###############################################################################
# cdebootstrap or debootstrap
###############################################################################
set(DEBOOTSTRAP_NAMES cdebootstrap debootstrap)
required_program(DEBOOTSTRAP 
  "${DEBOOTSTRAP_NAMES}"
  "Program for bootstrapping debian")

###############################################################################
# kpartx
###############################################################################
required_program(KPARTX 
  kpartx
  "Program to map image files to devices")

###############################################################################
# wget
###############################################################################
required_program(WGET 
  wget
  "Program for downloading files")

###############################################################################
# fdisk
###############################################################################
required_program(FDISK 
  fdisk
  "Program for creating partitions")

###############################################################################
# dd
###############################################################################
required_program(DD 
  dd
  "Program for creating and writing image files")

###############################################################################
# chroot
###############################################################################
required_program(CHROOT
  chroot
  "Program for switching to a new linux root")

###############################################################################
# qemu_arm_static
###############################################################################
required_program(QEMU_ARM_STATIC
  qemu-arm-static
  "ARM emulator for bootstrapping inside chroot")

###############################################################################
# mkfs.ext4
###############################################################################
required_program(MKFS_EXT4
  mkfs.ext4
  "Program for making ext4 filesystems")

###############################################################################
# mkfs.vfat
###############################################################################
required_program(MKFS_VFAT
  mkfs.vfat
  "Program for making vfat filesystems")


###############################################################################
# Configure the gnss-rpi scripts
###############################################################################


function (executable_file name)
configure_file(${name}.in
  ${CMAKE_BINARY_DIR}${CMAKE_FILES_DIRECTORY}/${name}
  @ONLY)
file(COPY ${CMAKE_BINARY_DIR}${CMAKE_FILES_DIRECTORY}/${name}
  DESTINATION ${CMAKE_BINARY_DIR}
  FILE_PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ
  GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)

endfunction()

executable_file(gnss-rpi)
executable_file(run)
executable_file(apt-update)
executable_file(bootstrap)
executable_file(cleanup)
executable_file(compilation)
executable_file(final)
executable_file(make-image)
executable_file(mount-fs)
executable_file(packages.list)
executable_file(cmdline.txt)
executable_file(fstab)
executable_file(hostname)
executable_file(interfaces)
executable_file(debconf.set)

install(PROGRAMS
  ${CMAKE_BINARY_DIR}/run
  ${CMAKE_BINARY_DIR}/apt-update
  ${CMAKE_BINARY_DIR}/bootstrap
  ${CMAKE_BINARY_DIR}/cleanup
  ${CMAKE_BINARY_DIR}/compilation
  ${CMAKE_BINARY_DIR}/final
  ${CMAKE_BINARY_DIR}/make-image
  ${CMAKE_BINARY_DIR}/mount-fs
  DESTINATION lib/gnss-rpi)

install(FILES
  ${CMAKE_BINARY_DIR}/packages.list
  ${CMAKE_BINARY_DIR}/cmdline.txt
  ${CMAKE_BINARY_DIR}/fstab
  ${CMAKE_BINARY_DIR}/hostname
  ${CMAKE_BINARY_DIR}/interfaces
  ${CMAKE_BINARY_DIR}/debconf.set
  DESTINATION lib/gnss-rpi)
  

install(PROGRAMS ${CMAKE_BINARY_DIR}/gnss-rpi DESTINATION sbin)
