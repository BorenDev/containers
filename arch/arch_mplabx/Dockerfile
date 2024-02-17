FROM archlinux:base-devel

ARG MPLABX_VERSION=6.20
ARG XC_TOOLCHAIN=xc32
ARG XC_TOOLCHAIN_VERSION=4.35
ARG ATPACK_NAME=PIC32MZ-EF_DFP
ARG ATPACK_VERSION=1.4.168
ARG SIM_ATPACK_NAME=Simulator_TP
ARG SIM_ATPACK_VERSION=1.7.660
ARG CURIOSITY_ATPACK_NAME=PKOB4_TP
ARG CURIOSITY_ATPACK_VERSION=1.14.1168
ARG USER_NAME=developer
ARG USER_ID=1000
ARG GROUP_ID=1000

# ------------------------------------------------------------------------------
# Install required packages
RUN pacman --noconfirm -Syyu \
  && pacman --noconfirm -S \
  unzip tar cmake autoconf automake curl make libusb bash ninja git \
  mingw-w64-toolchain

# ------------------------------------------------------------------------------
# Setup for MPLABX and XC32
# It appears microchip didn't like the automated downloads and have added another
# step to the download which prevents this curl from working.
# RUN curl -fSL -A "Mozilla/4.0" -o /tmp/mplabx-installer.tar \ "https://ww1.microchip.com/downloads/aemDocuments/documents/DEV/ProductDocuments/SoftwareTools/MPLABX-v${MPLABX_VERSION}-linux-installer.tar"
COPY MPLABX-v${MPLABX_VERSION}-linux-installer.tar /tmp/mplabx-installer.tar
RUN tar xf /tmp/mplabx-installer.tar -C /tmp/ && rm /tmp/mplabx-installer.tar \
  && USER=root "./tmp/MPLABX-v${MPLABX_VERSION}-linux-installer.sh" --nox11 \
  -- --unattendedmodeui none --mode unattended \
  && rm "./tmp/MPLABX-v${MPLABX_VERSION}-linux-installer.sh" \
  && rm -rf /opt/microchip/mplabx/v${MPLABX_VERSION}/packs/arm \
  && rm -rf /opt/microchip/mplabx/v${MPLABX_VERSION}/packs/Microchip \
  && mkdir -p /opt/microchip/mplabx/v${MPLABX_VERSION}/packs/Microchip \
  && rm -rf "/opt/microchip/mplabx/v${MPLABX_VERSION}/mplab_platform/browser-lib"
ENV PATH /opt/microchip/mplabx/v${MPLABX_VERSION}/mplab_platform/bin:$PATH
ENV PATH /opt/microchip/mplabx/v${MPLABX_VERSION}/mplab_platform/mplab_ipe:$PATH
ENV XCLM_PATH /opt/microchip/mplabx/v${MPLABX_VERSION}/mplab_platform/bin/xclm

# Not sure i need this...?
# RUN /opt/microchip/mplabx/v${MPLABX_VERSION}/mplab_platform/bin/xclm -netfile <PORT> 'SERVER_HOST_URL'; exit 0

# It appears microchip didn't like the automated downloads and have added another
# step to the download which prevents this curl from working.
# RUN curl -fSL -A "Mozilla/4.0" -o /tmp/${TOOLCHAIN}.run \
#   "https://ww1.microchip.com/downloads/aemDocuments/documents/DEV/ProductDocuments/SoftwareTools/${TOOLCHAIN}-v${TOOLCHAIN_VERSION}-full-install-linux-x64-installer.run" \
COPY xc32-v${XC_TOOLCHAIN_VERSION}-full-install-linux-x64-installer.run /tmp/${XC_TOOLCHAIN}.run
RUN chmod a+x "/tmp/${XC_TOOLCHAIN}.run" \
  && "/tmp/${XC_TOOLCHAIN}.run" --mode unattended --unattendedmodeui none \
  --netservername localhost --LicenseType FreeMode \
  && rm "/tmp/${XC_TOOLCHAIN}.run" \
  && rm -rf /opt/microchip/mplabx/v${MPLABX_VERSION}/packs/arm \
  && rm -rf /opt/microchip/mplabx/v${MPLABX_VERSION}/packs/Microchip \
  && mkdir -p /opt/microchip/mplabx/v${MPLABX_VERSION}/packs/Microchip

ENV PATH /opt/microchip/${XC_TOOLCHAIN}/v${XC_TOOLCHAIN_VERSION}/bin:$PATH

# RUN /opt/microchip/${TOOLCHAIN}/v${TOOLCHAIN_VERSION}/bin/xclm -netfile <PORT> 'SERVER_HOST_URL'; exit 0
# Run free license script
COPY cpluslic.sh /tmp/cpluslic.sh
RUN bash /tmp/cpluslic.sh

# RUN curl -fSL -A "Mozilla/4.0" -o /tmp/tmp-pack.atpack \
#   "https://packs.download.microchip.com/Microchip.PIC32MZ-EF_DFP.1.4.168.atpack" \
#   && mkdir -p /opt/microchip/mplabx/v${MPLABX_VERSION}/packs/PIC32MZ-EF_DFP/1.4.168 \
#   && unzip -o /tmp/tmp-pack.atpack -d /opt/microchip/mplabx/v${MPLABX_VERSION}/packs/PIC32MZ-EF_DFP/1.4.168 \
#   && rm /tmp/tmp-pack.atpack

# Cleanup packs


ENV CUR_ATPACK_NAME ${ATPACK_NAME}
ENV CUR_ATPACK_VER ${ATPACK_VERSION}
ENV CUR_ATPACK_DIR "/opt/microchip/mplabx/v${MPLABX_VERSION}/packs/Microchip/${CUR_ATPACK_NAME}/${CUR_ATPACK_VER}"

ENV INSTALL_ATPACK Microchip.${CUR_ATPACK_NAME}.${CUR_ATPACK_VER}.atpack
ENV TMP_ATPACK /tmp/tmp-pack.atpack

COPY "${INSTALL_ATPACK}" "${TMP_ATPACK}"
RUN mkdir -p "${CUR_ATPACK_DIR}" \
  && unzip -o ${TMP_ATPACK} -d "${CUR_ATPACK_DIR}" \
  && rm ${TMP_ATPACK}


ENV CUR_ATPACK_NAME ${SIM_ATPACK_NAME}
ENV CUR_ATPACK_VER ${SIM_ATPACK_VERSION}
ENV CUR_ATPACK_DIR "/opt/microchip/mplabx/v${MPLABX_VERSION}/packs/Microchip/${CUR_ATPACK_NAME}/${CUR_ATPACK_VER}"

ENV INSTALL_ATPACK Microchip.${CUR_ATPACK_NAME}.${CUR_ATPACK_VER}.atpack
ENV TMP_ATPACK /tmp/tmp-pack.atpack

COPY "${INSTALL_ATPACK}" "${TMP_ATPACK}"
RUN mkdir -p "${CUR_ATPACK_DIR}" \
  && unzip -o ${TMP_ATPACK} -d "${CUR_ATPACK_DIR}" \
  && rm ${TMP_ATPACK}


ENV CUR_ATPACK_NAME ${CURIOSITY_ATPACK_NAME}
ENV CUR_ATPACK_VER ${CURIOSITY_ATPACK_VERSION}
ENV CUR_ATPACK_DIR "/opt/microchip/mplabx/v${MPLABX_VERSION}/packs/Microchip/${CUR_ATPACK_NAME}/${CUR_ATPACK_VER}"

ENV INSTALL_ATPACK Microchip.${CUR_ATPACK_NAME}.${CUR_ATPACK_VER}.atpack
ENV TMP_ATPACK /tmp/tmp-pack.atpack

COPY "${INSTALL_ATPACK}" "${TMP_ATPACK}"
RUN mkdir -p "${CUR_ATPACK_DIR}" \
  && unzip -o ${TMP_ATPACK} -d "${CUR_ATPACK_DIR}" \
  && rm ${TMP_ATPACK}


WORKDIR /src



# NOTES
# # ------------------------------------------------------------------------------
# # Create $USER_NAME user...
# RUN groupadd -g "${GROUP_ID}" "${USER_NAME}" \
#   && useradd -m --uid "${USER_ID}" --gid "${GROUP_ID}" "${USER_NAME}" \
#   && echo "${USER_NAME} ALL=(ALL) NOPASSWD: /usr/sbin/pacman, /usr/sbin/makepkg" >> /etc/sudoers \
#   && mkdir -p /workdir \
#   && chown "${USER_NAME}:users" /workdir
# ##  mingw-w64-binutils \
# ##  mingw-w64-crt \
# ##  mingw-w64-gcc \
# ##  gdb-msvc-git \
# ##  mingw-w64-headers \
# ##  mingw-w64-ucrt-x86_64-libwinpthread-git \
# ##  mingw-w64-ucrt-x86_64-make \
# ##  mingw-w64-ucrt-x86_64-pkgconf \
# ##  mingw-w64-ucrt-x86_64-tools-git \
# ##  mingw-w64-ucrt-x86_64-winpthreads-git \
# ##  mingw-w64-ucrt-x86_64-winstorecompat-git 


