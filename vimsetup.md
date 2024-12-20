# Complete Vim IDE Setup Guide

### For CentOS Stream 9 | Vim 9.1| Last Updated: December 2024


## Table of Contents


1. Installation Steps
1. Vim Configuration File
1. Usage Guide
1. Troubleshooting


## Installation Steps


1. Install required dependencies:

bash
```
[*Install Node.js (required for CoC*)]: #
sudo dnf install nodejs npm

[*Install ripgrep (required ofr FZF searchin*)]: #
sudo dnf install ripgrep

[*Install language servers*]: #
sudo dnf install -g typescript typescript-language-server
pip3 install python-language-server
```

1. Install vim-plug


bash

```
==curl== -fLo ~/.vim/autoload/plug.vim --create-dirs \
        <https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim>
```
1. Copy the entire configuration below to your ==~/.vimrc== file
1. Open Vim and run ==:PlugInstall===

### Vim Configuration File

vim
```
" Vim IDE Configuration for CentOS Stream 9
" Last Updated: December 2024

" ========== Basic Settings ==========
set nocompatible              " Be iMproved, required
filetype off                  " Required for Vundle
set encoding=UTF-8            " Set UTF-8 encoding
set number                    " Show line numbers
set relativenumber            " Show relative line numbers
set mouse=a                   " Enable mouse support
set cursorline               " Highlight current line
set expandtab                " Use spaces instead of tabs
set tabstop=4                " 4 spaces for tabs
set shiftwidth=4             " 4 spaces for indentation
set smartindent              " Smart autoindenting
set hidden                   " Allow hidden buffers
set updatetime=300           " Faster completion
set timeoutlen=500           " By default timeoutlen is 1000 ms
set clipboard=unnamedplus    " Use system clipboard
set signcolumn=yes          " Always show signcolumn

" ========== Plugin Manager (Vim-Plug) ==========
" Install vim-plug if not found
if empty(glob('~/.vim/autoload/plug.vim'))
  silent !curl -fLo ~/.vim/autoload/plug.vim --create-dirs
    \ https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
endif

" Run PlugInstall if there are missing plugins
autocmd VimEnter * if len(filter(values(g:plugs), '!isdirectory(v:val.dir)'))
  \| PlugInstall --sync | source $MYVIMRC
\| endif

call plug#begin('~/.vim/plugged')

" ========== Theme ==========
Plug 'dracula/vim', { 'as': 'dracula' }    " Dracula theme
Plug 'joshdick/onedark.vim'                " Atom One Dark theme

" ========== File Explorer ==========
Plug 'preservim/nerdtree'                  " File explorer
Plug 'ryanoasis/vim-devicons'              " Icons for NERDTree
Plug 'tiagofumo/vim-nerdtree-syntax-highlight'  " Syntax highlighting for NERDTree

" ========== Status Line ==========
Plug 'vim-airline/vim-airline'             " Status line
Plug 'vim-airline/vim-airline-themes'       " Airline themes

" ========== Language Server Protocol ==========
Plug 'neoclide/coc.nvim', {'branch': 'release'}  " Conquer of Completion
Plug 'antoinemadec/coc-fzf'                      " FZF integration for CoC

" ========== Git Integration ==========
Plug 'tpope/vim-fugitive'                  " Git wrapper
Plug 'airblade/vim-gitgutter'              " Git diff in sign column

" ========== Language Support ==========
Plug 'sheerun/vim-polyglot'                " Language pack
Plug 'fatih/vim-go'                        " Go support
Plug 'rust-lang/rust.vim'                  " Rust support
Plug 'keith/swift.vim'                     " Swift support
Plug 'ap/vim-css-color'                    " CSS color preview

" ========== Code Completion ==========
Plug 'jiangmiao/auto-pairs'                " Auto pairs for brackets
Plug 'tpope/vim-surround'                  " Surround vim
Plug 'preservim/nerdcommenter'             " Easy commenting

" ========== Search ==========
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }  " Fuzzy finder
Plug 'junegunn/fzf.vim'                              " FZF integration

" ========== Window Management ==========
Plug 'christoomey/vim-tmux-navigator'      " Seamless navigation

call plug#end()

" ========== Theme Configuration ==========
syntax enable
colorscheme dracula
set background=dark
let g:airline_theme='dracula'
let g:airline_powerline_fonts = 1

" ========== NERDTree Configuration ==========
nnoremap <C-n> :NERDTreeToggle<CR>
let NERDTreeShowHidden=1
let g:NERDTreeGitStatusWithFlags = 1

" ========== CoC Extensions ==========
let g:coc_global_extensions = [
  \ 'coc-json',
  \ 'coc-tsserver',
  \ 'coc-html',
  \ 'coc-css',
  \ 'coc-python',
  \ 'coc-java',
  \ 'coc-rust-analyzer',
  \ 'coc-go',
  \ 'coc-clangd',
  \ 'coc-sql',
  \ 'coc-xml',
  \ 'coc-prettier'
  \ ]

" ========== CoC Configuration ==========
" Use tab for trigger completion with characters ahead and navigate
inoremap <silent><expr> <TAB>
      \ coc#pum#visible() ? coc#pum#next(1):
      \ CheckBackspace() ? "\<Tab>" :
      \ coc#refresh()
inoremap <expr><S-TAB> coc#pum#visible() ? coc#pum#prev(1) : "\<C-h>"

" Make <CR> auto-select the first completion item
inoremap <silent><expr> <CR> coc#pum#visible() ? coc#pum#confirm()
                              \: "\<C-g>u\<CR>\<c-r>=coc#on_enter()\<CR>"

function! CheckBackspace() abort
  let col = col('.') - 1
  return !col || getline('.')[col - 1]  =~# '\s'
endfunction

" GoTo code navigation
nmap <silent> gd <Plug>(coc-definition)
nmap <silent> gy <Plug>(coc-type-definition)
nmap <silent> gi <Plug>(coc-implementation)
nmap <silent> gr <Plug>(coc-references)

" ========== FZF Configuration ==========
nnoremap <C-p> :Files<CR>
nnoremap <C-f> :Rg<CR>

" ========== Split Navigation ==========
nnoremap <C-J> <C-W><C-J>
nnoremap <C-K> <C-W><C-K>
nnoremap <C-L> <C-W><C-L>
nnoremap <C-H> <C-W><C-H>

" ========== Terminal Configuration ==========
" Open terminal in vertical split
nnoremap <leader>t :vertical terminal<CR>

" ========== Auto Commands ==========
" Automatically format on save for specific filetypes
autocmd BufWritePre *.js,*.jsx,*.ts,*.tsx,*.py,*.go,*.rs,*.cpp,*.c,*.h,*.java Prettier

" Return to last edit position when opening files

autocmd BufReadPost *
     \ if line("'\"") > 0 && line("'\"") <= line("$") |
     \   exe "normal! g`\"" |
     \ endif

```

### Usage Guide

#### Essential Keyboard Shortcuts

##### File Navigation

+ ==Ctrl + n==: Toggel NERDTree file explorer
+ ==Ctrl + p==: Fuzzy file Search
+ ==Ctrl + f==: Full Text search in project

#### Window Management

+ ==Ctrl + h/j/k/l==: Navigate between splits
+ ==:vs==: Vertical split
+ ==:sp==: Horizontal split
+ ==leader + t==: Open terminal in vertical split 

#### Code Navigation

+ ==gd==: Go to definition
+ ==gy==: Go to type definition
+ ==gi==: Go to Implementation
+ ==gr==: Go to references
+ ==k==: Show documentation

#### Code Completion

+ ==Tab==: Navigation completion menu
+ ==Shift + Tab==: Navigate completion menu backwards
+ ==Enter==: Select completion item


#### Git Commands

+ ==:Git==: Access git Commands
+ ==:Gstatus==: View git Status
+ ==:Gdiff==: View git diff


#### Basic Commands

+ ==:w==: Save file
+ ==:q==: Quit
+ ==:wq==: Save and Quit
+ ==:e filename==: Open file
+ ==:bd==: close buffers


### Language-Specific Features

#### Python

- Automatic Completion
- Code formatting with black
- Import sorting
- Documentation on hover


#### C/C++

- Completion vi clangd
- Header/source switching
- Cmake integration


#### JavaScript/TypeScript
- Automatic imports
- Code formatting with Prettier
- ESLint integration


#### Go 

- Auto imports
- Code formatting
- Documentation lookup


#### Customizaion Tips

#### Changing Thems

Add to your ~/.vimrc:

vim
```
colorscheme onedark "Or any other installed Them"

```

#### Adding New Plugins 

1. Add new Plug entries between ==call plug#begin() and callplug#end()==
1. Run ==:PlugInstall==

#### Adding Language Servers

1. Install the language server
1. Add corresponding coc-extension to ==g:coc_global_extensions==
1. Restart Vim

### Troubleshooting

If you encouter issues:

 1. Check Dependencies:
    - Run ==node --version== to verify Node.js Installation
    - Check if vim-plug is installed in ~/.vim/autoload/
    - Verify language servers are installed

 1. Common Solutions:
    - Run ==:checkhealth== to identify problems
    - Check ==:messages== for errors
    - Update plugins with ==:PlugUpdate
    - Run ==:CocInfo== to check CoCstatus

 1. Plugin-Specific Issues:
    - NERDTree: Run ==:NERDTreeToggle to check for errors
    - CoC:Check ==:CocConfi for proper configuration
    - FZF:Verify ripgrep installation with ==rg --version==
    
 1. If problems persist:
    - Remove ~/.vim/plugged/ directory and reinstall plugins
    - Check system logs for any relevant errors
    - Verify all required packages are installed via dnf

Remember to always backup your existing .vimrc before making changes!
            
