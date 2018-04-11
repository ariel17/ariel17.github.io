.. title: Fixing home and end keys in Vim
.. slug: fixing-home-and-end-keys-in-vim
.. date: 2016-12-29 01:03:49 UTC-03:00
.. tags: vim, arch, linux
.. category: 
.. link: http://ianrolfe.livejournal.com/39474.html
.. description: 
.. type: text

Adding this to `~/.vimrc` file fixes incorrect HOME & END key behavior:

.. code-block:: vim

   " Home key
   imap <esc>OH <esc>0i
   cmap <esc>OH <home>
   nmap <esc>OH 0

   " End key
   nmap <esc>OF $
   imap <esc>OF <esc>$a
   cmap <esc>OF <end>
