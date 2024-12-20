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
[Install Node.js (required for CoC)]: #
sudo dnf install nodejs npm

[Install ripgrep (required for FZF searching)]: #
sudo dnf install ripgrep

[Install language servers]: #
sudo dnf install -g typescript typescript-language-server
pip3 install python-language-server

```

1. Install vim-plug


bash

```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
        <https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim>
```
1. Copy the entire configuration below to your <mark>**~/.vimrc**</mark> file
1. Open Vim and run <mark>:**PlugInstall**</mark>

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

+ `Ctrl + n`: Toggel NERDTree file explorer
+ `Ctrl + p`: Fuzzy file Search
+ `Ctrl + f`: Full Text search in project

#### Window Management

+ ``Ctrl + h/j/k/l``: Navigate between splits
+ ``:vs``: Vertical split
+ ``:sp``: Horizontal split
+ ``leader + t``: Open terminal in vertical split 

#### Code Navigation

+ ``gd``: Go to definition
+ ``gy``: Go to type definition
+ ``gi``: Go to Implementation
+ ``gr``: Go to references
+ ``k`` : Show documentation

#### Code Completion

+ ``Tab``: Navigation completion menu
+ ``Shift + Tab``: Navigate completion menu backwards
+ ``Enter``: Select completion item


#### Git Commands

+ ``:Git``: Access git Commands
+ ``:Gstatus``: View git Status
+ ``:Gdiff``: View git diff


#### Basic Commands

+ ``:w``: Save file
+ ``:q``: Quit
+ ``:wq``: Save and Quit
+ ``:e filename``: Open file
+ ``:bd``: close buffers


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

`colorscheme onedark "Or any other installed Them"`

#### Adding New Plugins 

1. Add new Plug entries between ``call plug#begin() and callplug#end()``
1. Run ``:PlugInstall``

#### Adding Language Servers

1. Install the language server
1. Add corresponding coc-extension to ``g:coc_global_extensions``
1. Restart Vim

### Troubleshooting

If you encouter issues:

 1. Check Dependencies:
    - Run **node --version** to verify Node.js Installation
    - Check if vim-plug is installed in **~/.vim/autoload/**
    - Verify language servers are installed

 1. Common Solutions:
    - Run ``:checkhealth`` to identify problems
    - Check ``:messages`` for errors
    - Update plugins with **:PlugUpdate**
    - Run ``:CocInfo`` to check CoCstatus

 1. Plugin-Specific Issues:
    - NERDTree: Run ``:NERDTreeToggle`` to check for errors
    - ``CoC:Check`` :CocConfi for proper configuration
    - FZF: Verify ripgrep installation with ``rg --version``
    
 1. If problems persist:
    - Remove **~/.vim/plugged/** directory and reinstall plugins
    - Check system logs for any relevant errors
    - Verify all required packages are installed via dnf

Remember to always backup your existing .vimrc before making changes!


# Adding Preview Functionality to Your Vim IDE

## Step 1: Install Required Dependencies


bash

```
# Install yarn (needed for markdown-preview.nvim)
sudo npm install -g yarn

# Install instant-markdown-d
sudo npm -g install instant-markdown-d

# Install firefox if not already installed (Optional)
sudo dnf install firefox xdg-utils

```
## Step 2: Add new Plugins

Add these lines in your ``~/.vimrc`` file ust before the ``call plug#end()`` line, in the plugins setion:

vim

```
" ===== NEW PLUGINS TO ADD =====
" Preview Support
Plug 'iamcco/markdown-preview.nvim', { 'do': 'cd app && yarn install' }  " Markdown preview
Plug 'turbio/bracey.vim', {'do': 'npm install --prefix server'}         " Live HTML/CSS preview
Plug 'suan/vim-instant-markdown'                                        " Instant Markdown preview
Plug 'prettier/vim-prettier', {
  \ 'do': 'yarn install --frozen-lockfile --production',
  \ 'for': ['javascript', 'typescript', 'css', 'less', 'scss', 'json', 'graphql', 'markdown', 'vue', 'yaml', 'html'] }


```
## Step 3: Add Configuration Settings

Add these lines to your `.vimrc` file after all other configurations (at the end of the file):

vim 

```
" ========== Preview Configuration ==========
" Markdown Preview Configuration
" set to 1, nvim will open the preview window after entering the markdown buffer
let g:mkdp_auto_start = 0

" set to 1, the nvim will auto close current preview window when change
" from markdown buffer to another buffer
let g:mkdp_auto_close = 1

" set to 1, the vim will refresh markdown when save the buffer or
" leave from insert mode, default 0 is auto refresh markdown as you edit or
" move the cursor
let g:mkdp_refresh_slow = 0

" specify browser to open preview page
let g:mkdp_browser = ''

" set to 1, echo preview page url in command line when open preview page
let g:mkdp_echo_preview_url = 1

" Bracey Configuration (HTML/CSS Live Preview)
let g:bracey_browser_command='firefox'
let g:bracey_auto_start_browser=1
let g:bracey_refresh_on_save=1

" Instant Markdown Configuration
let g:instant_markdown_autostart = 0
let g:instant_markdown_browser = "firefox"
let g:instant_markdown_mathjax = 1

" Prettier Configuration
let g:prettier#autoformat = 1
let g:prettier#autoformat_require_pragma = 0

" ========== Preview Keybindings ==========
" Markdown Preview
nmap <C-m> <Plug>MarkdownPreviewToggle

" HTML/CSS Preview (Bracey)
nmap <C-b> :Bracey<CR>
nmap <C-S-b> :BraceyStop<CR>
nmap <C-r> :BraceyReload<CR>


```

## Step 4: Install the New Plugins

1. Save your updated `.vimrc`
1. Open vim
1. Run `:PlugInstall`
1. Wait for all installations to Complete
1. Restart your Vim

## Usage Guide
 
### Markdown Preview

- [x] Start preview: `Ctrl + m` or `:MarkdownPreview`
- [x] Stop preview: `:MarkdownPreviewStop`
- [x] Instant preview: `:InstantMardownPreview`

### HTML/CSS Preview

- [x] Start preview: `Ctrl + b` or `:Bracey`
- [x] Stop preview: `Ctrl + Shift + b` or `:BraceyStop`
- [x] Reload preview: `Ctrl + r` or `:BraceyReload`
- [x] Preview updates in real-timeoutlen


### Prettier Integration

- [x] Auto-formats supported files on save
- [x] Manual format: `:PrettierAsync`
- [x] Supports multiples file types (JavaScript, TypeScript, CSS, HTML,etc.)

### Troubleshooting 

If you encouter issues:


  1. Verify dependencies:


bash

```
yarn --version
npm --version
firefox --version

```
  1. Common fixes:

  - [x] Run `:checkhealth` in Vim to identify issues
  - [x] Try reinstalling plugins: `:PlugClean` followed by `:PlugInstall`
  - [x] Check browser settings if previews don't open
  - [x] Ensure all npm packages are installed globally

  1. Plugin-specific issues:

  - [x] For markdown-preview: Try running `cd ~/.vimrc/plugged/markdown-preview.nvim/app && yarn install`
  - [x] For Brace: Check if the server is installed with `ls ~/.vimrc/plugged/bracey.vim/server/node_modules`


### Notes

- [x] Previews open in Firefox by default(can be changed in configuration)
- [x] Live preview updates might have a slight delay dependining on the file size
- [x] Some features require an active internet connection(for CDN resources)
- [x] Preview windows automatically close when switching buffers (configurable)



