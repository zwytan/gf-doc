### Web Server

    func GetServer(names ...string) *Server
    func (s *Server) BindController(pattern string, c Controller) error
    func (s *Server) BindControllerMethod(pattern string, c Controller, method string) error
    func (s *Server) BindControllerRest(pattern string, c Controller) error
    func (s *Server) BindHandler(pattern string, handler HandlerFunc) error
    func (s *Server) BindObject(pattern string, obj interface{}) error
    func (s *Server) BindObjectRest(pattern string, obj interface{}) error
    func (s *Server) Domain(domain string) *Domain
    func (s *Server) GetName() string
    func (s *Server) NotFound(w http.ResponseWriter, r *http.Request)
    func (s *Server) ResponseStatus(w http.ResponseWriter, code int)
    func (s *Server) Run() error
    func (s *Server) SetAddr(addr string) error
    func (s *Server) SetConfig(c ServerConfig) error
    func (s *Server) SetErrorLog(logger *log.Logger) error
    func (s *Server) SetIdleTimeout(t time.Duration) error
    func (s *Server) SetIndexFiles(index []string) error
    func (s *Server) SetIndexFolder(index bool) error
    func (s *Server) SetMaxHeaderBytes(b int) error
    func (s *Server) SetPort(port int) error
    func (s *Server) SetReadTimeout(t time.Duration) error
    func (s *Server) SetServerAgent(agent string) error
    func (s *Server) SetServerRoot(root string) error
    func (s *Server) SetTLSConfig(tls *tls.Config) error
    func (s *Server) SetWriteTimeout(t time.Duration) error


### 多域名支持