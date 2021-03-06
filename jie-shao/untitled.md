# spring-cloud-commons

![](../.gitbook/assets/image%20%281%29.png)

### @EnableDiscoveryClient

这个注解是找DiscoveryClient接口的实现。

 [Spring Cloud Netflix Eureka](https://cloud.spring.io/spring-cloud-netflix/), [Spring Cloud Consul Discovery](https://cloud.spring.io/spring-cloud-consul/), and [Spring Cloud Zookeeper Discovery](https://cloud.spring.io/spring-cloud-zookeeper/).都是通过实现DiscoveryClient接口来自动发现的。



负载均衡的RestTemplate

[https://blog.csdn.net/u012702547/article/details/77940838](https://blog.csdn.net/u012702547/article/details/77940838)



```text
@Configuration
public class MyConfiguration {

    @LoadBalanced
    @Bean
    RestTemplate restTemplate() {
        return new RestTemplate();
    }
}

public class MyClass {
    @Autowired
    private RestTemplate restTemplate;

    public String doOtherStuff() {
        String results = restTemplate.getForObject("http://stores/stores", String.class);
        return results;
    }
}
```

负载均衡主要实现是在下面这个拦截器中。

拦截器中注入了LoadBalancerClient的实现，当一个被@LoadBalanced注解修饰的RestTemplate对象向外发起HTTP请求时，会被LoadBalancerInterceptor类的intercept方法拦截，在这个方法中直接通过getHost方法就可以获取到服务名（因为我们在使用RestTemplate调用服务的时候，使用的是服务名而不是域名，所以这里可以通过getHost直接拿到服务名然后去调用execute方法发起请求）。  


```text

package org.springframework.cloud.client.loadbalancer;import java.io.IOException;import java.net.URI;import org.springframework.http.HttpRequest;import org.springframework.http.client.ClientHttpRequestExecution;import org.springframework.http.client.ClientHttpRequestInterceptor;import org.springframework.http.client.ClientHttpResponse;import org.springframework.util.Assert;/** * @author Spencer Gibb * @author Dave Syer * @author Ryan Baxter * @author William Tran */public class LoadBalancerInterceptor implements ClientHttpRequestInterceptor {   private LoadBalancerClient loadBalancer;   private LoadBalancerRequestFactory requestFactory;   public LoadBalancerInterceptor(LoadBalancerClient loadBalancer, LoadBalancerRequestFactory requestFactory) {      this.loadBalancer = loadBalancer;      this.requestFactory = requestFactory;   }   public LoadBalancerInterceptor(LoadBalancerClient loadBalancer) {      // for backwards compatibility      this(loadBalancer, new LoadBalancerRequestFactory(loadBalancer));   }   @Override   public ClientHttpResponse intercept(final HttpRequest request, final byte[] body,         final ClientHttpRequestExecution execution) throws IOException {      final URI originalUri = request.getURI();      String serviceName = originalUri.getHost();      Assert.state(serviceName != null, "Request URI does not contain a valid hostname: " + originalUri);      return this.loadBalancer.execute(serviceName, requestFactory.createRequest(request, body, execution));   }} 
```

