
## websockify: WebSockets support for any application/server

See: [novnc/websockify](https://github.com/novnc/websockify)

# Added by Shellrent

Added support for Tokens in Cookies in `websocketproxy.py` on `get_target`


```python
    def get_target(self, target_plugin, path):
        """
        Parses the path, extracts a token, and looks up a target
        for that token using the token plugin. Sets
        target_host and target_port if successful
        """
        # The files in targets contain the lines
        # in the form of token: host:port
		
        token = ''
		
		# Extract the token parameter from cookie
        if 'Cookie' in self.headers:
            cookiestr = self.headers['Cookie']
            cookiestr.replace( 'Cookie: ', '', 1 )
            cookies = cookiestr.split('; ')

            for cookie in cookies:
                cookie = cookie.split('=')
                if 'token' in cookie[0]:
                    token = cookie[1]
                    break

        # Extract the token parameter from url
        if token == '':
            args = parse_qs(urlparse(path)[4]) # 4 is the query from url

            if not 'token' in args or not len(args['token']):
                raise self.server.EClose("Token not present")

            token = args['token'][0].rstrip('\n')

        if token == '':
            raise self.server.EClose( "Empty Token defined" )

        # Search for the token
        result_pair = target_plugin.lookup( token )

        if result_pair is not None:
            return result_pair
        else:
            raise self.server.EClose( "Token '%s' not found" % token )
```
