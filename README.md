```sh
# download, package and install prebuilt release of clangd-indexer:
makepkg-mingw -LR --install

# or package and install separately
makepkg-mingw -LR
pacman -U *.pkg.tar.zst

# remove
pacman -R ${MINGW_PACKAGE_PREFIX}-clangd-indexing-tools
```
