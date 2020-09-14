# VKD3D (HansKristian & Doitsujin fork edition :frog: ) - git version, multilib with userpatches support

https://github.com/HansKristian-Work/vkd3d-proton - Fork of https://source.winehq.org/git/vkd3d.git/ (to build that version, see customization.cfg)

You might need https://github.com/Frogging-Family/spirv-tools-git to build this package

You can use your own vkd3d patches by putting them in the same folder as the PKGBUILD and giving them the .myvkd3dpatch extension.
You can also revert vkd3d patches by putting them in the same folder as the PKGBUILD and giving them the .myvkd3drevert extension.


```
git clone https://github.com/Frogging-Family/vkd3d-git.git
cd vkd3d-git
makepkg -si
```

