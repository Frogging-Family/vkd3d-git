# VKD3D - git version, multilib with userpatches support

https://source.winehq.org/git/vkd3d.git/

You might need https://github.com/Frogging-Family/spirv-tools-git to build this package

For vkd3d-proton, it's recommended to use a standalone build you can get with https://github.com/Frogging-Family/dxvk-tools.

You can use your own vkd3d patches by putting them in the same folder as the PKGBUILD and giving them the .myvkd3dpatch extension.
You can also revert vkd3d patches by putting them in the same folder as the PKGBUILD and giving them the .myvkd3drevert extension.


```
git clone https://github.com/Frogging-Family/vkd3d-git.git
cd vkd3d-git
makepkg -si
```

