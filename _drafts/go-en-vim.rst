.. title: Go en ViM
.. slug: go-en-vim
.. date: 2017-06-02 15:24:21 UTC-03:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Estoy armando mi ambiente local para programar en Go. Estoy analizando quizás
comenzar a usar |vsc|_, pero como fallback quiero dejar ViM correctamente
configurado. Se asume que |vundle|_ y |nerdtree|_ ya está instalados.

Dependencias del sistema operativo
----------------------------------

* ViM v7.4
* Exhuberant ctags v5.8
* Go v1.8.3
* Go tags: `go get -u github.com/jstemmer/gotags`

Plugins a usar
--------------

* |vim-go|_
* |neocomplete|_

Configuraciones en .vimrc

.. code:: vim

   syntax enable  
   filetype plugin on  
   set number  
   let g:go_disable_autoinstall = 0
   
   " Highlight
   let g:go_highlight_functions = 1  
   let g:go_highlight_methods = 1  
   let g:go_highlight_structs = 1  
   let g:go_highlight_operators = 1  
   let g:go_highlight_build_constraints = 1  
   
   " Autocomplete
   let g:neocomplete#enable_at_startup = 1
   
   " Tags
   let g:tagbar_type_go = {  
       \ 'ctagstype' : 'go',
       \ 'kinds'     : [
           \ 'p:package',
           \ 'i:imports:1',
           \ 'c:constants',
           \ 'v:variables',
           \ 't:types',
           \ 'n:interfaces',
           \ 'w:fields',
           \ 'e:embedded',
           \ 'm:methods',
           \ 'r:constructor',
           \ 'f:functions'
       \ ],
       \ 'sro' : '.',
       \ 'kind2scope' : {
           \ 't' : 'ctype',
           \ 'n' : 'ntype'
       \ },
       \ 'scope2kind' : {
           \ 'ctype' : 't',
           \ 'ntype' : 'n'
       \ },
       \ 'ctagsbin'  : 'gotags',
       \ 'ctagsargs' : '-sort -silent'
   \ }

Todo junto
----------

Ejecutando `:GoInstallBinaries` vim-go_ termina de instalar los binarios
necesarios para ser usados dentro de ViM.

.. |vsc| replace:: Visual Studio Code
.. _vsc: https://code.visualstudio.com/

.. |vundle| replace:: VundleVim/Vundle.vim
.. _vundle: https://github.com/VundleVim/Vundle.vim

.. |nerdtree| replace:: NERD Tree
.. _nerdtree: https://github.com/scrooloose/nerdtree

.. |vim-go| replace:: fatih/vim-go
.. _vim-go: https://github.com/fatih/vim-go

.. |neocomplete| replace:: Shougo/neocomplete.vim
.. _neocomplete: https://github.com/Shougo/neocomplete.vim
