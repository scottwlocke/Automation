FROM mcr.microsoft.com/powershell:lts-debian-11

# [Option] Install zsh
ARG INSTALL_ZSH="true"
# [Option] Upgrade OS packages to their latest versions
ARG UPGRADE_PACKAGES="true"

# Install Oh-My-Posh
ARG INSTALL_OHMYPOSH="true"

# Install needed packages and setup non-root user. Use a separate RUN statement to add your own dependencies.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
COPY library-scripts/*.sh /tmp/library-scripts/


RUN apt-get update \
    && /bin/bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" "true" "true" \
    && chsh "${USERNAME}" -s "$(which pwsh)" \
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts

# Install PowerShell Modules
RUN pwsh -Command Set-PSRepository -Name PSGallery -InstallationPolicy Trusted && \
    # Pester
    pwsh -Command Install-Module -Name Pester -Scope AllUsers -Repository PSGallery && \
    # InvokeBuild
    pwsh -Command Install-Module -Name InvokeBuild -Scope AllUsers -Repository PSGallery && \
    # PlatyPS
    pwsh -Command Install-Module -Name PlatyPS -Scope AllUsers -Repository PSGallery && \
    # PSScriptAnalyzer
    pwsh -Command Install-Module -Name PSScriptAnalyzer -Scope AllUsers -Repository PSGallery && \
    # PSTeams
    pwsh -Command Install-Module -Name PSTeams -Scope AllUsers -Repository PSGallery && \
    # Terminal-Icons - Requires Nerd Font Install
    pwsh -Command Install-Module -Name Terminal-Icons -Scope AllUsers -Repository PSGallery && \
    # Set Gallery to Untrusted like default
    pwsh -Command Set-PSRepository -Name PSGallery -InstallationPolicy Untrusted

# [Optional] Uncomment this section to install additional packages.
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends fontconfig

# Install Oh-My-Posh for bash, zsh and pwsh
ENV POSH_THEME="/workspaces/oh-my-posh/themes/powerlevel10k_modern.omp.json"

# Install Oh-My-Posh
RUN mkdir /home/${USERNAME}/bin; \
    wget https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/posh-linux-$(dpkg --print-architecture) -O /bin/oh-my-posh; \
    chmod +x /bin/oh-my-posh; \
    chown ${USERNAME}: /bin; \
    # bash Theme
    sed -i 's/^__bash_prompt$/#&/' /home/${USERNAME}/.bashrc && \
    echo "eval \"\$(oh-my-posh init bash --config $POSH_THEME)\"" >> /home/${USERNAME}/.bashrc \
    # zsh Theme
    echo "eval \"\$(oh-my-posh init zsh --config $POSH_THEME)\"" >> /home/${USERNAME}/.zshrc

# pwsh Theme copy from file
# COPY Microsoft.PowerShell_profile.ps1 /home/${USERNAME}/.config/powershell/Microsoft.PowerShell_profile.ps1


# # Bash Shell - Oh-My-Posh
# RUN sed -i 's/^__bash_prompt$/#&/' /home/${USERNAME}/.bashrc && \
#     echo "eval \"\$(oh-my-posh init bash --config $POSH_THEME)\"" >> /home/${USERNAME}/.bashrc
# # Override vscode's own ZSH prompt with oh-my-posh:
# RUN echo "eval \"\$(oh-my-posh init zsh --config $POSH_THEME)\"" >> /home/${USERNAME}/.zshrc