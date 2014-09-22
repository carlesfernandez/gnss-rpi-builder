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
# resize2fs
###############################################################################
required_program(RESIZE2FS
  resize2fs
  "Program for resizing ext4 filesystems")

###############################################################################
# Configure the gnss-rpi scripts
###############################################################################


function (install_file name)
configure_file(${name}.in
  ${name}
  @ONLY)

endfunction()

install_file(gnss-rpi)
install_file(run)
install_file(apt-update)
install_file(bootstrap)
install_file(cleanup)
install_file(compilation)
install_file(final)
install_file(make-image)
install_file(mount-fs)
install_file(packages.list)
install_file(cmdline.txt)
install_file(fstab)
install_file(hostname)
install_file(interfaces)
install_file(debconf.set)
install_file(gnss-rpi-2dev)
install_file(hosts)

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
  ${CMAKE_BINARY_DIR}/hosts
  ${CMAKE_BINARY_DIR}/interfaces
  ${CMAKE_BINARY_DIR}/debconf.set
  DESTINATION lib/gnss-rpi)
  

install(PROGRAMS 
  ${CMAKE_BINARY_DIR}/gnss-rpi 
  ${CMAKE_BINARY_DIR}/gnss-rpi-2dev
  DESTINATION sbin)
