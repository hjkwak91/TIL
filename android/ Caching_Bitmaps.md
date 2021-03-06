* Android Developers 에서는 Glide추천

* 이미지를 빨리 로딩하고, 매번 새로 생성하는 것을 방지하기 위해 / 메모리 사용 컨트롤 위해 사용.
    * 다른 방법
        * recycleing the child views, view 떠날 때
        * the garbage collector fress up loaded bitmaps.

ListView나 GridView, ViewPager는 보통 여러 장의 이미지를 동시에 로딩하게 된다. 그리고 사용자가 스크롤할 때 화면에 보이지 않게 되는 이미지의 뷰는 재활용된다. 메모리가 충분하지 않기 때문에 이 전에 로딩된 이미지는 곧 가비지콜렉팅 될 것이다. 하지만 매번 다시 스크롤해서 올라갈 때마다 예전에 불렀던 이미지를 새로 다시 로딩하는 것은 네트워크가 느린 안드로이드 폰에서는 효율적이지 못하다. 



이러한 문제점 때문에 메모리나 디스크 캐시를 사용한다. 이미지를 캐싱하는 것은 매번 새롭게 이미지가 필요할 때마다 로딩하는 것이 아니라 메모리에 캐싱해서 로딩하지 않고 바로 가져다 쓰는 것을 말한다. 매번 로딩하는 것이 아니기 때문에 메모리 측면에서도 시간 측면에서도 효율적인 방법이다.



안드로이드에서는 매모리 캐시는 LruCache를 사용하고, 디스크 캐시는 DiskLruCache를 사용한다. 

* 메모리 캐시
    * LruCache는 LinkedHashMap을 사용한다. 최근에 사용된 객체의 strong reference를 보관하고 있다가 정해진 캐시 사이즈가 오버되면 가장 최근에 사용되지 않은 것부터 제거하는 LRU 알고리즘을 사용하는 메모리 캐시다. 
    이 전에는 bitmap cache에 SoftReference나 WeakReference를 사용하는 방식을 썼지만 안드로이드 2.3(진저브레드)부터 가비지콜렉터가 공격적으로 가비지콜렉팅을 하면서 비효율적인 방법이 됐다. 게다가 3.0이전 버전에서는 메모리 해제를 제대로 못해서 충돌문제도 있었다. 이러한 문제 때문에 LruCache를 사용한다.
    먼저 LruCache의 캐시 사이즈를 정하기 위해서는 몇 가지 요소들을 고려해야 한다. 해당 어플리케이션에서 얼마나 메모리를 사용할 것지, 얼마나 많은 이미지를 하나의 화면에 보여줘야하고, 또 얼마나 준비되어야 하는 지 이외에도 화면 해상도, 각 이미지의 해상도, 이미지 접근빈도 등 여러가지가 있다.
    어느 정도의 캐시 사이즈가 적당하다는 공식은 없기 때문에 어플리케이션의 메모리 사용량을 측정해보면서 개발자가 적절하게 정해야 한다. 너무 사이즈를 작게하면 오버헤드가 발생하게 되고 사이즈를 너무 크게 잡게되면 OutOfMemory Exception이 될 수 있다.


* 디스크 캐시
   * 일반적으로 메모리 캐시와 디스크 캐시의 차이는 메모리 캐시는 처리 속도가 빠르지만 저장 공간이 작고, 디스크 캐시는저장 공간은 상대적으로 크지만 파일 입출력으로 인한 처리 속도가 느리다. 
  이러한 특성때문에 메모리 캐시는 빠르기는 하지만 메모리 용량이 크지 않아서 메모리 캐시만으로도 부족하다면 디스크캐시를 사용해야한다. 그리고 만약 작업 중에 전화가 오거나 앱이 백그라운드로 가면 메모리 캐시가 사라져버린다. 반면에 디스크 캐시를 사용하면 데이터가 지속적으로 남아있고 용량도 더 쓸 수 있다. 물론 메모리캐시보다 느리지만 매번 네트워크에서 읽어와 로딩하는 것보다는 빠르기 때문에 사용한다.
  
  메모리 캐시에서 이미지 로딩은 메인 스레드에서 진행하지만 디스크 캐시는 백그라운드에서 로딩한다. 디스크캐시 작업은 언제 끝날지 모르기 때문에 백그라운드에서 사용한다.


[참고](http://meylady.tistory.com/49)
