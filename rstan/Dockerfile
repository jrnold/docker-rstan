FROM rocker/verse:latest
MAINTAINER Jeffrey Arnold jeffrey.arnold@gmail.com

ENV STANVERSION 2.18.1

RUN apt-get update \
	&& apt-get install -y --no-install-recommends apt-utils ed libnlopt-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/

RUN apt-get update && apt-get install -y \
    clang-4.0 \
    llvm-4.0 \
    postgresql-client \
    jags \
    libc++-dev
RUN ln -s /usr/bin/clang++-4.0 /usr/bin/clang++

# Global site-wide config -- neeeded for building packages
RUN mkdir -p $HOME/.R/ \
	&& echo "CC=clang-4.0" >> $HOME/.R/Makevars \
	&& echo "CXX=clang++-4.0" >> $HOME/.R/Makevars \
	&& echo "CXX14=clang++-4.0" >> $HOME/.R/Makevars \
    && echo "CXXFLAGS=-O2 -mtune=native -march=native -Wno-unused-variable -Wno-unused-function -Wno-unknown-pragmas -Wno-unused-local-typedefs \n" >> $HOME/.R/Makevars

# Install rstan
RUN install2.r --error rstan --deps TRUE \
	bayesplot \
    rstanarm \
    rjags \
    rstantools \
    shinystan \
    tidybayes \
    ggmcmc \
    ggeffects \
    && rm -rf /tmp/downloaded_packages/ /tmp/*.rds

# Config for rstudio user
RUN echo "rstan::rstan_options(auto_write = TRUE)\n" >> /home/rstudio/.Rprofile \
    && echo "options(mc.cores = parallel::detectCores())\n" >> /home/rstudio/.Rprofile

WORKDIR /opt
RUN wget https://github.com/stan-dev/cmdstan/releases/download/v${STANVERSION}/cmdstan-${STANVERSION}.tar.gz && \
	tar -xzf cmdstan-${STANVERSION}.tar.gz
WORKDIR /opt/cmdstan-${STANVERSION}
RUN mkdir -p $HOME/.config/cmdstan \
	&& echo "CXX=clang++-4.0" >> $HOME/.config/cmdstan/make.local
RUN make build && ln -fs /opt/cmdstan-${STANVERSION}/bin/stanc /usr/bin
