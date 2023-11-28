# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# CONTRIBUTORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS WITH
# THE SOFTWARE.

# shellcheck disable=SC2148

CLANG=${MINGW_PACKAGE_PREFIX}-clang
NAME=clangd-indexing-tools

export license=spdx:Apache-2.0
export makedepends=${CLANG}
export mingw_arch=clang64
export pkgbase=mingw-w64-${NAME}
export pkgdesc='clangd-indexing-tools (mingw-w64)'
export pkgname="${MINGW_PACKAGE_PREFIX}-${NAME}"

RELEASE=2
UNDERSCORE=$(echo -n ${NAME} | tr '-' '_')
URL=https://github.com/clangd/clangd
VERSION='17.0.3'

export pkgrel=${RELEASE}
export pkgver=${VERSION}
export sha256sums='7d3d0f45ffc9e1ea6cb268070c8f0ae834b13c424624ba4525ff86cda407533b'
export source=${URL}/releases/download/${VERSION}/${UNDERSCORE}-windows-${VERSION}.zip
export url=${URL}

LIB_START=$(echo "${CLANG} ${MINGW_PREFIX}/" | wc -m)

IFS=$'\n' read -rd '' -a CLANG_LIB <<<"$(
    pacman -Ql "${CLANG}" |
        grep --color=never lib/ |
        cut -c "${LIB_START}"- |
        sort
)"

_start=0

# the release archive includes files that are already present in the clang
# package, and therefore conflicts with said package when trying to install.
# this function checks if a file from the archive is present in the pacman query
# output
#
# NOTE: both `pacman` and `find lib/` outputs are sorted to save a lot of cpu
# cycles, increasing packaging time
includes() {
    for ((_index = "${_start}"; _index < ${#CLANG_LIB[@]}; ++_index)); do
        echo TEST "${CLANG_LIB[_index]}" == "${1}"

        if [[ "${CLANG_LIB[_index]}" == "${1}" ]]; then
            _start=$((_index + 1))
            return 0
        fi
    done

    return 1
}

package() {
    cd "${srcdir}"/clangd_${VERSION} || return 1

    TARGET="${pkgdir}${MINGW_PREFIX}"

    echo FIND INSTALL BIN
    find bin/ -type f -exec install -Dm644 "{}" "${TARGET}/{}" \;

    echo FIND INSTALL LIB
    find lib/ -type f -print0 | IFS= sort -z | while IFS= read -rd '' i; do
        if ! includes "${i}"; then
            install -Dm644 "${i}" "${TARGET}/${i}"
        fi
    done

    LICENSE_TXT=LICENSE.TXT
    LICENSES=share/licenses/${NAME}

    install -Dm644 "${LICENSE_TXT}" "${TARGET}/${LICENSES}/${LICENSE_TXT}"
}
