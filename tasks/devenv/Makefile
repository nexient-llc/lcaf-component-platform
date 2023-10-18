export
OS := $(shell uname -s)

DOCKER ?= docker
COMPOSE ?= docker compose

DTIME := $(shell date +'%s')

# Windows
#  open Powershell and run
#  > wsl --list
#  if empy
#  run
#  > wsl --install
# sudo apt update && sudo apt install -y build-essential

.PHONY: platform/devenv/configure
platform/devenv/configure:
	@echo $(OS)
	@if [ "${OS}" = "Darwin" ]; then \
		echo "Configuring local dev environment for macOS" && \
		$(MAKE) platform/devenv/configure-mac; \
	elif [ "${OS}" = "Linux" ]; then \
		echo "Configuring local dev environment for linux or WSL" && \
		$(MAKE) platform/devenv/configure-linux-wsl; \
	else \
		$(MAKE) platform/devenv/not-supported; \
	fi

.PHONY: platform/devenv/configure-mac
platform/devenv/configure-mac: DHOME = $(HOME)/.docker
platform/devenv/configure-mac: platform/devenv/configure-common
	# macOS specific configure steps here
	@echo "Your local dev env is configured for macOS"

.PHONY: platform/devenv/configure-linux-wsl
platform/devenv/configure-linux-wsl: DHOME = $(shell wslpath -a $$(cmd.exe /C 'echo %USERPROFILE%'))/.docker
platform/devenv/configure-linux-wsl: platform/devenv/linux-setup platform/devenv/configure-common
	# linux specific configure steps here
	@echo "Your local dev env is configured for linux or WSL"

.PHONY: platform/devenv/not-supported
platform/devenv/not-supported:
	@echo "ERROR: Operating system not supported!"
	@echo "INFO:  Local dev setup is currently only supported for macOS, linux or WSL on Windows"

#Target to initialize the repo
.PHONY: platform/devenv/configure-common
platform/devenv/configure-common: platform/devenv/configure-shell platform/devenv/configure-global-pkgs platform/devenv/configure-local-pkgs platform/devenv/configure-docker-buildx

.PHONY: platform/devenv/linux-setup
platform/devenv/linux-setup:
	@sudo apt-get update && \
	sudo -s apt-get install -y build-essential --fix-missing && \
	default_shell="$$(grep "^$$USER:" /etc/passwd | cut -d: -f7)" && \
	if [ $${default_shell} = "/bin/bash" ]; then \
		sudo -s apt-get install -y zsh && \
		ZSH_PATH=$$(which zsh) && \
		sudo chsh -s $$ZSH_PATH $$USER; \
	fi && \
	sudo apt-get install -y python3.10-venv; \
	touch $${HOME}/.zshrc;

.PHONY: platform/devenv/configure-shell
platform/devenv/configure-shell:
	@if [ -d $${HOME}/.dso_zsh ]; then \
		rm -rf $${HOME}/.dso_zsh; \
	fi
	@git clone https://github.com/nexient-llc/dso-zsh.git $${HOME}/.dso_zsh;
	@if [ ! -d $${HOME}/.m2 ]; then \
		mkdir -p $${HOME}/.m2; \
	fi
	@cp -p $${HOME}/.dso_zsh/settings.xml $${HOME}/.m2/settings.xml
	@if [ ! -d $${HOME}/.oh-my-zsh ]; then \
		rm -rf $${HOME}/.oh-my-zsh; \
		unset ZSH && curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh | /bin/bash; \
		git clone https://github.com/zdharma-continuum/fast-syntax-highlighting.git $${HOME}/.oh-my-zsh/custom/plugins/fast-syntax-highlighting; \
		git clone https://github.com/djui/alias-tips.git $${HOME}/.oh-my-zsh/custom/plugins/alias-tips; \
	fi
	@if [ -f $${HOME}/.zshrc ]; then \
		mv $${HOME}/.zshrc $${HOME}/.zshrc-$$(date +'%s'); \
	fi
	@cp $${HOME}/.dso_zsh/.zshrc $${HOME}/.zshrc;
	@if [ -d $${HOME}/.dso_magicdust ]; then \
		rm -rf $${HOME}/.dso_magicdust; \
	fi
	@git clone https://github.com/nexient-llc/magicdust.git $${HOME}/.dso_magicdust;
	@if [ -d $${HOME}/.dso_magicdust ]; then \
		cd $${HOME}/.dso_magicdust; \
		python3 -m venv $${HOME}/.venv-dso; \
		source $${HOME}/.venv-dso/bin/activate; \
		python setup.py install; \
		pip install . ; \
		echo "source $${HOME}/.venv-dso/bin/activate" >> $${HOME}/.zshrc ; \
		echo "source $${HOME}/.venv-dso/bin/activate" >> $${HOME}/.bashrc ; \
	fi

.PHONY: platform/devenv/configure-global-pkgs
platform/devenv/configure-global-pkgs:
	@curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh | /bin/bash
	@BREW='eval "$$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"'; \
	if [ "${OS}" = "Darwin" ]; then \
		if [ "$$(/usr/bin/uname -m)" = "arm64" ]; then \
			BREW='eval "$$(/opt/homebrew/bin/brew shellenv)"'; \
		else \
			BREW='eval "$$(/usr/local/bin/brew shellenv)"'; \
		fi; \
	fi; \
	eval $$BREW; \
	echo $$BREW >> $${HOME}/.bashrc; \
	echo $$BREW >> $${HOME}/.zshrc; \
	brew install gcc unzip jq yq aws-sso-util coursier jinja2-cli fzf aws-sso-util

.PHONY: platform/devenv/configure-docker-buildx
platform/devenv/configure-docker-buildx:
	-$(DOCKER) buildx create --use

# Target to add asdf plugins listed in .tool-versions
# this doesn't work to actually install and use asdf in the same make iteration since asdf 
# requires sourcing complex shell scripts to add functions to your environment, which is 
# way beyond what make is designed to do.  install asdf before using this makefile.
.PHONY:
platform/devenv/configure-local-pkgs:
	# @if [ ! -d $${HOME}/.asdf ] || [ ! -d $${HOME}/.asdf/.git ]; then \
	# 	git clone https://github.com/asdf-vm/asdf.git $${HOME}/.asdf --branch v0.11.3; \
	# fi
	# ASDF='. "$${HOME}/.asdf/asdf.sh"' && \
	# echo $$ASDF >> $${HOME}/.bashrc; \
	# ASDF_COMPLETIONS='. "$${HOME}/.asdf/completions/asdf.bash"' && \
	# echo $$ASDF_COMPLETIONS >> $${HOME}/.bashrc; \
	# source $${HOME}/.bashrc && \
	# cut -d' ' -f1 .tool-versions | xargs -I{} asdf plugin add {}
	# asdf install
