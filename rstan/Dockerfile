FROM rocker/verse:latest
MAINTAINER Jeffrey Arnold jeffrey.arnold@gmail.com

ENV STANVERSION 2.18.0

RUN apt-get update \
	&& apt-get install -y --no-install-recommends apt-utils ed libnlopt-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/

RUN apt-get update && apt-get install -y \
    clang-3.9 \
    postgresql-client \
    libc++-dev
RUN ln -s /usr/bin/clang++-3.9 /usr/bin/clang++

# Global site-wide config -- neeeded for building packages
RUN mkdir -p $HOME/.R/ \
	&& echo "CC=clang-3.9" >> $HOME/.R/Makevars \
	&& echo "CXX=clang++-3.9" >> $HOME/.R/Makevars \
    && echo "CXXFLAGS=-O3 -mtune=native -march=native -Wno-unused-variable -Wno-unused-function -flto -ffat-lto-objects  -Wno-unused-local-typedefs \n" >> $HOME/.R/Makevars

# Install rstan
RUN install2.r --error --deps TRUE \
    rstan \
    && rm -rf /tmp/downloaded_packages/ /tmp/*.rds

# Config for rstudio user
RUN mkdir -p $HOME/.R/ \
	&& echo "CC=clang-3.9" >> $HOME/.R/Makevars \
	&& echo "CXX=clang++-3.9" >> $HOME/.R/Makevars \
    && echo "CXXFLAGS=-O3 -mtune=native -march=native -Wno-unused-variable -Wno-unused-function -flto -ffat-lto-objects  -Wno-unused-local-typedefs -Wno-ignored-attributes -Wno-deprecated-declarations\n" >> $HOME/.R/Makevars \
    && echo "rstan::rstan_options(auto_write = TRUE)\n" >> /home/rstudio/.Rprofile \
    && echo "options(mc.cores = parallel::detectCores())\n" >> /home/rstudio/.Rprofile

# Install rstan
RUN install2.r --error --deps TRUE \
	loo \
	bayesplot \
    rstanarm \
    rstantools \
    shinystan \
    tidybayes \
    ggmcmc \
    && rm -rf /tmp/downloaded_packages/ /tmp/*.rds

WORKDIR /opt
RUN wget https://github.com/stan-dev/cmdstan/releases/download/v${STANVERSION}/cmdstan-${STANVERSION}.tar.gz && \
	tar -xzf cmdstan-${STANVERSION}.tar.gz
WORKDIR /opt/cmdstan-${STANVERSION}
RUN make build -j4 && ln -fs /opt/cmdstan-${STANVERSION}/bin/stanc /usr/bin
