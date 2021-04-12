配置 ShiroFilterFactoryBean 拦截器，注册的bean名称：shiroFilter

shiro执行流程：
1、OAuth2Filter.onAccessDenied
	1.1 从请求头HttpServletRequest.getHeader("token") 或 请求参数HttpServletRequest.getParameter("token") 中获取token
		未获取到token -> json{401: invalid token}，返回给前端401错误json错误
		获取到token -> executeLogin(request, response); 
			OAuth2Filter.createToken() 将token封装OAuth2Token对象，OAuth2Realm校验OAuth2Token是否合法
				OAuth2Realm.doGetAuthenticationInfo(AuthenticationToken) 校验 token
					校验成功 SimpleAuthenticationInfo info = new SimpleAuthenticationInfo(user, accessToken, getName())
					校验失败 throw new IncorrectCredentialsException("token失效，请重新登录")
						OAuth2Filter.onLoginFailure 捕获到登入失败异常，返回给前端401错误json错误json{401: throwable.getMessage()}

2、前端响应拦截

```javascript
/**
 * 响应拦截
 */
http.interceptors.response.use(response => {
  if (response.data && response.data.code === 401) { // 401, token失效
    clearLoginInfo()
    router.push({ name: 'login' })
  }
  return response
}, error => {
  return Promise.reject(error)
})


/**
 * 清除登录信息
 */
export function clearLoginInfo () {
  Vue.cookie.delete('token')
  store.commit('resetStore')
  router.options.isAddDynamicMenuRoutes = false
}
```
