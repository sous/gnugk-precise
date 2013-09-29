PTLIBVER:=v2.10.9
UNDERSCORE_PTLIBVER:=v2_10_9

all: dependencies grab update timed_build

dependencies:
	@make dependencies_`uname -s`

dependencies_Darwin:
	@if [ ! -d /usr/local/Cellar/subversion ]; then \
	  brew install subversion ; \
	fi
	@if [ ! -d /usr/local/Cellar/net-snmp ]; then \
	  brew install net-snmp ; \
	fi
	@if [ ! -d /usr/local/Cellar/libssh2 ]; then \
	  brew install libssh2 ; \
	fi

dependencies_Linux:
	@if ! grep -e '^deb-src' /etc/apt/sources.list > /dev/null ; then cp -f /etc/apt/sources.list /tmp/sources.list; sed -e 's/^deb /deb-src/' < /tmp/sources.list >> /etc/apt/sources.list; sudo apt-get update; fi
	@sudo apt-get update
	@sudo apt-get -f -y install build-essential subversion cvs libsnmp-dev libssh-dev liblua5.2-dev checkinstall
	@sudo apt-get -f -y build-dep ptlib
	@sudo apt-get -f -y build-dep h323plus
	@sudo apt-get -f -y build-dep gnugk
	@sudo apt-get -f -y install ccache

grab:
	@if [ ! -d ptlib-$(PTLIBVER) ] ; then svn co http://svn.code.sf.net/p/opalvoip/code/ptlib/tags/$(UNDERSCORE_PTLIBVER) ptlib-$(PTLIBVER)/ ; fi
	@if [ ! -f ~/.cvspass ]; then echo '/1 :pserver:anonymous@h323plus.cvs.sourceforge.net:2401/cvsroot/h323plus A' > ~/.cvspass; echo '/1 :pserver:anonymous@openh323gk.cvs.sourceforge.net:2401/cvsroot/openh323gk A' >> ~/.cvspass ; fi
	@if [ ! -d h323plus ] ; then cvs -z3 -d:pserver:anonymous@h323plus.cvs.sourceforge.net:/cvsroot/h323plus co -P h323plus ; fi
	@if [ ! -d openh323gk ]; then cvs -z3 -d:pserver:anonymous@openh323gk.cvs.sourceforge.net:/cvsroot/openh323gk co -P openh323gk ; fi

update:
	@cd ptlib-$(PTLIBVER) && svn update
	@cd h323plus && cvs update
	@cd openh323gk && cvs update

timed_build:
	@bash -c 'time make build'

build:
	@make ptlib-build
	@make h323plus-build
	@make openh323gk-build

ptlib-build:
	#make -C ptlib-$(PTLIBVER) clean
	cd ptlib-$(PTLIBVER) && \
	export PTLIBDIR=`pwd` && \
	./configure --disable-alsa \
                    --disable-oss \
                    --disable-v4l \
                    --disable-avc \
                    --disable-dc \
                    --disable-sdl \
	            --enable-lua \
                    --enable-odbc \
                    --enable-exceptions \
	            --enable-ipv6 \
                    --enable-openssl && \
        CC="ccache gcc" CXX="ccache g++" make -j4 debugnoshared

h323plus-build:
	if [ -f h323plus/openh323u.mak ]; then rm -f h323plus/openh323u.mak ; fi
	export PTLIBDIR=`pwd`/ptlib-$(PTLIBVER) && \
	export OPENH323DIR=`pwd`/h323plus && \
	export CFLAGS=-I`pwd`/h323plus/include && \
	cd h323plus && \
	./configure --enable-h350 \
	            --enable-plugins \
	            --enable-h46019m \
	            --enable-h46019 \
	            --enable-h46023 \
	            --enable-h46026 \
	            --enable-h4609 \
	            --enable-h460p \
	            --enable-h235 \
	            --enable-h235-256 \
	            --enable-h249 \
	            --enable-h460p \
	            --enable-h460pre \
	            --enable-h460com \
	            --enable-h46017 && \
	CC="ccache gcc" CXX="ccache g++" make -j4 debugnoshared debugstatic

openh323gk-build:
	#make -C openh323gk clean
	export PTLIBDIR=`pwd`/ptlib-$(PTLIBVER) && \
	export OPENH323DIR=`pwd`/h323plus && \
	export CFLAGS=-I`pwd`/h323plus/include && \
	cd openh323gk && \
	./configure --with-pgsql-include-dir=/usr/include/postgresql \
	            --with-large-fdset=32767 \
	            --enable-h46017 \
	            --enable-h46018 \
	            --enable-h46023 \
	            --enable-sqlite \
	            --enable-mysql \
	            --enable-pgsql \
	            --enable-libssh \
	            --enable-lua \
	            --enable-unixodbc \
	            --enable-h46023 \
	            --with-libssh-dir=/usr \
	            --disable-netsnmp && \
	CC="ccache gcc" CXX="ccache g++" make -j4 debugstatic debugnoshared

install:
	cd openh323gk && sudo checkinstall

clean:
	make -C ptlib-$(PTLIBVER)/ clean
	make -C openh323gk/ clean
	make -C h323plus/ clean
