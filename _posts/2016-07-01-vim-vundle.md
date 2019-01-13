---
layout: post
title:  "[Linux][vim] vundle管理插件, astyle"
date:   2016-07-01
comments: true
categories: Linux
tags: Linux,vim
description: ""
published: true
---

## vim 常用插件


### Vundle 安装命令：

Vundle 是一个vim插件，它可以管理其它的vim插件，比如说帮你下载安装。

[https://github.com/VundleVim/Vundle.vim](https://github.com/VundleVim/Vundle.vim)


```
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

然后在 ~/.vimrc 中添加

```
" vundle Brief help
" :PluginList          -lists configured plugins
" :PluginInstall       - installs plugins; append '!' to update or just :PluginUpdate
" :PluginSearch foo    - search for foo; append '!' to refresh local cache
" :PluginClean         - confirms removal of unused plugins; append '!' to auto-approve removal
set nocompatible
filetype off

set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

Plugin 'VundleVim/Vundle.vim'

call vundle#end()

filetype plugin indent on
```

然后打开vim，执行 :PluginInstall 等待它自动从网上下载安装就可以了。

在最下面的状态栏可以看到当前处理的状态。



### auto-format 安装

autoformat 是一个vim插件，能够自动格式化c,c++文件。

[https://github.com/Chiel92/vim-autoformat#default-formatprograms](https://github.com/Chiel92/vim-autoformat#default-formatprograms)


因为上面我们已经安装了 vundle ，所以我们现在可以通过 vundle 来安装 auto-format 。

在 ~/.vimrc 中添加

```
Plugin 'Chiel92/vim-autoformat'
```

然后打开vim，执行 :PluginInstall 等待它自动从网上下载安装就可以了。

就是这么简单。


需要注意的是，vim-autoformat只是格式化程序框架，它依赖于代码风格检查工具来进行格式化。

我们需要单独安装这些工具：astyle (http://astyle.sourceforge.net/subversion.html)

```
svn  checkout  "https://svn.code.sf.net/p/astyle/code/trunk/AStyle"
cd AStyle/build/gcc
make
make install
```

然后 astyle 会默认安装在 /usr/bin 目录下。具体见(AStyle/doc/astyle.html)


astyle 常用的参数：

```
--style=ansi

--add-brackets(-j) : Add brackets to unbracketed one line conditional statements (e.g. 'if', 'for', 'while'...). The statement must be on a single line.

--pad-oper(-p) : Insert space padding around operators.

--align-reference=middle(-W2)

--align-pointer=middle(-k2)

--indent-namespaces(-N) : Add extra indentation to namespace blocks.

--indent-preproc-define(-w)

--indent-preproc-block(-xW) :

--indent-switches

--indent-cases(-K)
```

<hr>


完整的 ~/.vimrc

```

" vundle Brief help
" :PluginList          -lists configured plugins
" :PluginInstall       - installs plugins; append '!' to update or just :PluginUpdate
" :PluginSearch foo    - search for foo; append '!' to refresh local cache
" :PluginClean         - confirms removal of unused plugins; append '!' to auto-approve removal
set nocompatible
filetype off

set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

Plugin 'VundleVim/Vundle.vim'

Plugin 'fatih/vim-go'

Plugin 'Chiel92/vim-autoformat'

Plugin 'scrooloose/nerdtree'

call vundle#end()

filetype plugin indent on


" vim-autoformat
"""""""""""""""""""""""""""""""""""""""""
let g:formatterpath = ['/usr/bin', '']
noremap <F3> :Autoformat<CR>
"au BufWrite * :Autoformat  "when you save file, it will auto format your file.

let g:autoformat_autoindent = 0
let g:autoformat_retab = 0
let g:autoformat_remove_trailing_spaces = 0

" --style=ansi --add-brackets(-j) --pad-oper(-p)
" --align-reference=middle(-W2) --align-pointer=middle(-k2)
" --indent-namespaces(-N) --indent-preproc-define(-w)
" --indent-switches --indent-cases(-K) --indent-preproc-block(-xW)
let g:formatdef_jian = '"astyle --style=ansi -j -p -W2 -k2 -N -w -K -xW"'
let g:formatters_cpp = ['jian']
let g:formatters_java = ['jian']
let g:formatters_c = ['jian']

" autocmd FileType vim,tex let b:autoformat_autoindent=0
" gg=G
" :retab
" :RemoveTrailingSpaces









"common
""""""""""""""""""""""""""""""""""""""""""
if has("syntax")
    syntax enable
    syntax on
endif

if has('mouse')
    set mouse=a     " Enable mouse usage (all modes)
endif

filetype plugin on
filetype indent on

set autoread    "set to auto read when a file is changed from the outside.
set ambiwidth=double    "for some unicode special character.
set whichwrap=b,s,<,>,[,]
set nocompatible    "close vi compatible mode
set backspace=indent,eol,start
set history=100
set cursorline  "current line will have a underline.
set hlsearch        " hight light search
set number
set ruler   "display cursor position in the right corner of the vim window
set magic   "set magic on, for regular expression
set noautochdir
set nocompatible    " Use Vim defaults
set ignorecase      " ignore case
set incsearch       " display when you still typing
set wrapscan      " stop the scan at the end of file
set wrap
set hidden
set vb t_vb=    "close the voice when cmd error.

" Show autocomplete menus.
set wildmenu

" Minimal number of screen lines to keep above and below the cursor.
"set scrolloff=999


"indent
""""""""""""""""""""""""""""""""""""""""""
"set expandtab  "tab will auto changed to blank space, (Ctrl+V+tab will input real tab), Makefile command need tab.
set smartindent " (set si)
set smarttab
set tabstop=4    " tab stop (set ts=4)
set shiftwidth=4    " shift width
set softtabstop=4
set autoindent  " auto indent (set ai)
set wrap    "Wrap lines



map <F5> :set ts=4<CR>:set expandtab<CR>:%retab!<CR>
map <C-F5> :set ts=4<CR>:set noexpandtab<CR>:%retab!<CR>


" NERD tree
""""""""""""""""""""""""""""""""""""""""""""
map <F10> :NERDTreeToggle<CR>
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif

"fold
""""""""""""""""""""""""""""""""""""""""""
set foldenable
set foldmethod=syntax    "index, expr, marker, syntax, diff
"set foldclose=all
" use space to folden, zo:open the fold, zc:fold.
nnoremap <space> @=((foldclosed(line( '.' ))<0) ? 'zc' : 'zo')<CR>
set foldopen-=search  "don't open folds when I search
set foldopen-=undo    "don't open folds when I undo
set foldlevel=100    "don't folds when I open the file
"set foldcolumn=1   "set the fold column width


"Linebreak on 500 characters
""""""""""""""""""""""""""""""""""""""""""
set tw=500
set lbr
set fo+=mB  "support for chinese.


"set mapleader
let mapleader=","   "mapleader default is '/', <leader> is mapleader.
let g:mapleader=","

"use ,s to source .vimrc
map <silent> <leader>s :source ~/.vimrc<CR>

"use ,e to open .vimrc
map <silent> <leader>e :e ~/.vimrc<CR>

"auto source .vimrc after edit .vimrc
autocmd! bufwritepost .vimrc source ~/.vimrc

au BufNewFile,BufRead *.cpp,*.c,*.h set syntax=cpp11

"use , to remove highlight search
nmap <silent> <leader><CR> :noh<CR>
nmap <leader>w :w!<cr>


nnoremap <C-h> <C-w>h
nnoremap <C-j> <C-w>j
nnoremap <C-k> <C-w>k
nnoremap <C-l> <C-w>l
map <leader>v :vsplit<CR>

map <C-A> ggvGY
map! <C-A> <Esc>ggvGY

map <F2> <Esc>:tabnew<CR>
map <C-P> :tabprev<CR>
map <C-N> :tabnext<CR>
map <C-C> :tabclose<CR>

"nnoremap <F3> :g/^\s*$/d<CR> "this will delete all blank line in file.
"map <C-H> :hide<CR>
"map <C-J> :tn<CR>
"map <C-K> :tp<CR>
"map <C-L> :cd .<CR>

map \p i(<Esc>ea)<Esc>
map \c i{<Esc>ea}<Esc>
map \n :nohlsearch<CR>
```
