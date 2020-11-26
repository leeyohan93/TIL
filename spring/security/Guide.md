## 커스텀한 User(from DB) 연동 가이드 

## PasswordEncoder 빈 등록
- 비밀번호는 반드시 인코딩해서 저장해야하며 스프링 시큐리티가 bcrypt 타입의 PasswordEndoer를 기본으로 제공한다. 
```java
@Bean
public PasswordEncoder passwordEncoder() {
	return PasswordEncoderFactories.createDelegatingPasswordEncoder();
}

```

## UserDetailsService 구현
- UserDetailsService를 현재 사용하는 데이터페이스를 사용해서 구현한다.
- 스프리티 설정에 따로 추가하지 않아주더라도 UserDetailsService 타입의 빈으로 등록만 되어있으면 사용이 가능하다.
```java
@Service
public class CustomUserService implements UserDetailsService {
    
    @Autowired
    private CustomUserRepository customUserRepository;

    @Autowired
    private PasswordEncoder passwordEncoder;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        CustomUser customUser = customUserRepository.findByUserName(username); 
        if(customUser == null){
            throw new UsernameNotFoundException(username); 
        }

        return User.builder()
                        .username(customUser.getUsername())
                        .password(customUser.getPassword())
                        .roles(customUser.getRole())
                        .build();
    }

    // 유저 생성 시 PasswordEncoder를 통한 패스워드 인코딩
    public CustomUser create(CustomUser customUser){
        customUser.encodePassword(passwordEncoder);     // 내부에서 인코딩
        return customUserRepository.save(customUser);
    }
}
``` 
