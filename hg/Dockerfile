FROM            scratch
ADD             rootfs.tar /

# Setup and source bashrc for tab-complete, shell coloring
ADD             bashrc /root/.bashrc
ENV             HOME /root
ENV             ENV $HOME/.bashrc

ADD             hgrc /etc/mercurial/hgrc

CMD             ["/bin/sh"]
