우리가 보통 쓰는 싱글톤은 아래와 같은데요.



using System;
 
namespace SingletonExamples
{
    // Broken, non thread-save solution.
    // Don't use this code.
    public class Singleton
    {
        private static Singleton _instance;
 
        private Singleton() { }
 
        public static Singleton Instance
        {
            get
            {
                if (_instance == null)
                {
                    _instance = new Singleton();
                }
                return _instance;
            }
        }
    }
}




사실 위코드에는 함정이 있습니다. 

첫번째로는 thread-safe 코드가 아니라는 점입니다. 

그럼 이렇게 변경할 수 있겠군요





using System;
 
namespace SingletonExamples
{
    public class Singleton
    {
        private static Singleton _instance;
        private static readonly object _lock = new object();
 
        private Singleton() { }
 
        public static Singleton Instance
        {
            get
            {
                lock (_lock)
                {
                    if (_instance == null)
                    {
                        _instance = new Singleton();
                    }
                    return _instance;
                }
            }
        }
    }
}




그런데 말입니다. 

이렇게 코드를 작성하면 lock 인스턴스가 항상 요구되기 떄문에 성능이 떨어질 수 밖에 없습니다. 

이 문제는 잠금을 하기 전에 인스턴스가 null인지 묻고, 잠금 획득하고, 인스턴스가 null인지 다시 묻는다면 해결은 할 수 있습니다. 

그런데 글만 읽어도 그렇게 하기 싫으시죠? 코드가 더러워지니까요 ㅠ



이 부분을 해결할 수 있는 개념이 있습니다. 

바로 Lazy implementation인데요.



Lazy에 대해 간단히 말하자면 

이름에서 언뜻 유추할 수 있듯이 첫번째로 쓰이기 전까지는 생성을 미루는 개념입니다. 

.NET 4 이상부터 사용할 수 있구요, 스레드에 안전하다는 특징이 있습니다. 





using System;
 
namespace SingletonExamples
{
    public class Singleton
    {
        private static readonly Lazy<Singleton> instance =
        new Lazy<Singleton>(() => new Singleton());
 
        public static Singleton Instance
        {
            get
            {
                return instance.Value;
            }
        }
 
        private Singleton()
        {
        }
    }
}


위 코드는 싱글톤이지만, 사용하기 전까지는 인스턴스를 미리 생성하지 않고, 스레드에도 안전합니다. 

(private 으로 생성자를 만든 이유는 다른 곳에서 Singleton의 인스턴스를 생성하지 못하도록 막은 듯 하네요)





당연하게만 사용했던 싱글톤 디자인 패턴에 대해 다시한번 생각하게 해준 rubikscode 씨게 감사드리며 포스팅을 마칩니다.

감사합니다^^



출처: https://yoon90.tistory.com/31 [주제없는 윤로그]
