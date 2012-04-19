fuel-pagecache
==============

Static cache for Fuelphp framework

This is a port of Lysender's kohana module
https://github.com/lysender/pagecache

How to
======

1. Enable the package in your fuel instance

2. Create a 'cache' folder in your docroot

3. In your Controller_Template insert the following code (marked with ***)

    public function before()
    {
        *** $this->pagecache = new Pagecache();
        *** $this->pagecache->setResponse($this->response);
        *** $this->pagecache->setRequest($this->request);


        if ($this->auto_render === true)
        {            
            $this->template = \View::forge($this->template);
        }
    }   
	
    public function after($response)
    {
        if ($this->auto_render === true and ! $response instanceof \Response)
        {
            $response = $this->response;
            $response->body = $this->template;

            *** if ($this->pagecache->isCacheable()) {
            ***    $this->pagecache->cache($_SERVER['REQUEST_URI']);
            *** }
        }

        return $response;
    }  
    

4. Enable cache in your controller

	$this->_pagecache->enableCache();

3. Add to your .htaccess the following code

    # BEGIN Page cache

    RewriteRule ^/(.*)/$ /$1 [QSA]
    RewriteRule ^$ cache/index.html [QSA]
    RewriteRule ^([^.]+)/$ cache/$1/index.html [QSA]
    RewriteRule ^([^.]+)$ cache/$1/index.html [QSA]

    # END Page cache

    RewriteCond %{REQUEST_FILENAME} -s [OR]
    RewriteCond %{REQUEST_FILENAME} -l [OR]
    RewriteCond %{REQUEST_FILENAME} -f [OR]
    RewriteCond %{REQUEST_FILENAME} -d

    RewriteRule ^.*$ - [NC,L]
    RewriteRule ^.*$ index.php [NC,L]   

