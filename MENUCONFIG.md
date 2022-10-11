# Configuration and Menuconfig

- To add your own menu in kconfig, create a Kconfig.projbuild file inside the main folder
- The parameters will always have a CONFIG_ prefix
- You can add choices, int, bool, string values only
- It is possible to have a sdkconfig.defaults in the root folder to set or avoid sharign sensitive data over the sdkconfig file
- The proper way imo is to have 3 sdkconfig in the git: release, debug and default. And one sdkconfig file local to the developer workspace
- The sdkconfig.defaults creates the sdkconfig.* specified in the root CMakeLists.txt if it does not exist
