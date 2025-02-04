---
layout: post
title: 사용자 중심 성능 메트릭
authors:
  - philipwalton
date: 2019-11-08
description: 사용자 중심 성능 메트릭은 실제 사용자의 사이트 경험을 이해하고 실제 사용자에게 도움이 되는 방식으로 이러한 경험을 개선하는 데 중요한 도구입니다.
tags:
  - performance
  - metrics
---

성능이 얼마나 중요한지는 모두가 알고 있습니다. 그러나 성능을 개선하고 웹사이트를 "빠르게" 만든다는 것은 구체적으로 무엇을 의미할까요?

사실 성능은 상대적입니다.

- 동일한 사이트더라도 강력한 장치와 고속 네트워크를 보유한 사용자에게는 빠르고, 하위 장치와 느린 네트워크를 보유한 사용자에게는 느립니다.
- 두 사이트가 정확히 같은 시간에 로드를 완료하더라도, 하나는 더 빨리 로드되는 것처럼 *보일 수* 있습니다(끝까지 항목을 표시하기를 기다리지 않고 콘텐츠를 점진적으로 로드하는 경우).
- 사이트가 빠르게 로드되는 것처럼 *보이지만* 사용자 상호 작용에 느리게 응답하거나, 전혀 응답하지 않을 수 있습니다.

따라서 정확하고 정량적으로 측정 가능한 객관적인 기준으로 성능을 이야기하는 것이 중요하며 이러한 기준을 *메트릭*이라고 합니다.

그러나 메트릭이 객관적인 기준을 기반으로 하고 정량적으로 측정할 수 있다고 해서 반드시 유용하다는 의미는 아닙니다.

## 메트릭 정의

일반적으로 웹 성능은 <code>[load](https://developer.mozilla.org/en-US/docs/Web/API/Window/load_event)</code> 이벤트로 측정되곤 합니다. 그러나 <code>load</code>가 페이지 수명 주기에서 잘 정의된 순간이라고 하더라도, 사용자가 관심을 갖는 부분과 반드시 일치하는 것은 아닙니다.

예를 들어 한 서버가 즉시 "로드"되는 최소 페이지로 응답한 다음, `load` 이벤트가 발생한 후 몇 초가 지날 때까지 페이지에 콘텐츠를 가져오고 표시하는 것을 지연시킬 수 있습니다. 이러한 페이지는 기술적으로는 로드 시간이 빨라지지만, 사용자가 실제로 페이지 로드를 경험하는 방식과는 일치하지 않습니다.

지난 몇 년 동안 Chrome 팀원들은 [W3C Web Performance Working Group](https://www.w3.org/webperf/)과 협력하여 사용자가 웹 페이지 성능을 경험하는 방식을 보다 정확하게 측정하는 일련의 새로운 API 및 메트릭을 표준화하기 위해 노력해 왔습니다.

메트릭이 사용자에게 유의미할 수 있도록, 다음과 같은 몇 가지 주요 질문을 중심으로 메트릭을 구성했습니다.

<table id="questions">
  <tr>
    <td><strong>진행 여부</strong></td>
    <td>탐색이 성공적으로 시작되었습니까? 서버에서 응답이 있었습니까?</td>
  </tr>
  <tr>
    <td><strong>유용성</strong></td>
    <td>사용자가 참여할 수 있는 충분한 콘텐츠가 렌더링되었습니까?</td>
  </tr>
  <tr>
    <td><strong>사용 가능 여부</strong></td>
    <td>사용자가 페이지와 상호 작용할 수 있습니까, 아니면 페이지에서 다른 작업이 진행되고 있습니까?</td>
  </tr>
  <tr>
    <td><strong>쾌적함</strong></td>
    <td>상호 작용이 부드럽고 자연스러우며 지연과 버벅거림이 없습니까?</td>
  </tr>
</table>

## 메트릭 측정 방법

성능 메트릭은 일반적으로 다음과 같은 두 가지 방법으로 측정됩니다.

- **실험실:** 일관되고 통제된 환경에서 페이지 로드를 시뮬레이션하는 도구 사용
- **현장** : 실제 사용자가 실제로 페이지를 로드하고 페이지와 상호 작용하는 경우

둘 다 다른 것보다 좋거나 나쁘다고 말할 수는 없습니다. 사실 일반적으로 좋은 성능을 보장하기 위해서는 둘 다 활용하는 것이 좋습니다.

### 실험실

새로운 기능을 개발할 때는 실험실에서 성능을 테스트하는 것이 필수적입니다. 기능이 프로덕션에 출시되기 전에는 실제 사용자의 성능 특성을 측정할 수 없으므로 성능 회귀를 방지하기 위해서는 출시 전 실험실에서 테스트해보는 것이 가장 좋습니다.

### 현장

실험실 테스트는 성능에 대한 합리적인 대용물이지만, 모든 사용자가 사이트를 실제로 경험하는 방식을 반드시 반영한다고 볼 수는 없습니다.

사이트의 성능은 사용자의 장치 및 네트워크 상태는 물론, 사용자가 페이지와 상호 작용하는지 여부(또는 방법)에 따라 달라질 수 있습니다.

또한 페이지 로드가 결정적이지 않을 수도 있습니다. 예를 들어 맞춤형 콘텐츠나 광고를 로드하는 사이트는 사용자마다 성능 특성이 크게 달라지지만, 실험실 테스트로는 이러한 차이점을 포착하지 못합니다.

사이트가 사용자를 위해 어떻게 작동하는지 제대로 알 수 있는 유일한 방법은 해당 사용자가 사이트를 로드하고 상호 작용할 때 사이트의 성능을 실제로 측정하는 것입니다. 이러한 측정 유형을 일반적으로 [실제 사용자 모니터링](https://en.wikipedia.org/wiki/Real_user_monitoring)(Real User Monitoring, RUM)이라고 합니다.

## 메트릭 유형

사용자가 성능을 인식하는 방식과 관련해 몇 가지 다른 메트릭 유형이 있습니다.

- **인지 로드 속도:** 페이지가 얼마나 빠르게 모든 시각적 요소를 화면에 로드하고 렌더링할 수 있는지 확인합니다.
- **로드 응답성:** 구성 요소가 사용자 상호 작용에 빠르게 응답하기 위해 페이지에서 필요한 JavaScript 코드를 얼마나 빠르게 로드하고 실행할 수 있는지를 확인합니다.
- **런타임 응답성:** 페이지 로드 후 페이지가 사용자 상호 작용에 얼마나 빨리 응답할 수 있는지 확인합니다.
- **시각적 안정성:** 페이지의 요소가 사용자가 예상하지 못한 방식으로 이동해 잠재적으로 상호 작용을 방해하는지를 확인합니다.
- **원활함:** 전환 및 애니메이션이 일관된 프레임 속도로 렌더링되고 다음 상태로 유동적으로 흐르는지 확인합니다.

위와 같이 모든 유형의 성능 메트릭을 보면, 하나의 메트릭만으로는 페이지의 모든 성능 특성을 캡처하기에 충분하지 않다는 것을 알 수 있습니다.

## 측정할 중요 메트릭

- **[First Contentful Paint(최초 콘텐츠풀 페인트, FCP)](/fcp/) :** 페이지가 로드되기 시작한 시점부터 페이지 콘텐츠의 일부가 화면에 렌더링될 때까지의 시간을 측정합니다. *([실험실](#in-the-lab), [현장](#in-the-field))*
- **[Largest contentful paint(최대 콘텐츠풀 페인트, LCP)](/lcp/):** 페이지가 로드되기 시작한 시점부터 가장 큰 텍스트 블록 또는 이미지 요소가 화면에 렌더링될 때까지의 시간을 측정합니다. *([실험실](#in-the-lab), [현장](#in-the-field))*
- **[First input delay(최초 입력 지연, FID)](/fid/):** 사용자가 링크를 클릭하거나, 버튼을 탭하거나, 사용자 지정 JavaScript 기반 컨트롤을 사용하는 등 처음으로 상호 작용할 때부터 해당 상호 작용에 대한 응답으로 브라우저가 실제로 이벤트 핸들러 처리를 시작하기까지의 시간을 측정합니다. *([현장](#in-the-field))*
- **[Time to Interactive(상호 작용까지의 시간, TTI)](/tti/):** 페이지가 로드되기 시작한 시점부터 시각적으로 렌더링되고, 있는 경우 초기 스크립트가 로드되고, 사용자 입력에 신속하게 안정적으로 응답할 수 있는 시점까지의 시간을 측정합니다. *([실험실](#in-the-lab))*
- **[Total blocking time(총 차단 시간, TBT)](/tbt/):** 메인 스레드가 입력 응답을 막을 만큼 오래 차단되었을 때 FCP와 TTI 사이 총 시간을 측정합니다. *([실험실](#in-the-lab))*
- **[Cumulative layout shift(누적 레이아웃 이동, CLS)](/cls/):** 페이지 로드가 시작될 때와 해당 [수명 주기 상태](https://developers.google.com/web/updates/2018/07/page-lifecycle-api)가 숨김으로 변경될 때 사이에 발생하는 모든 예기치 않은 레이아웃 이동의 누적 점수를 측정합니다. *([실험실](#in-the-lab), [현장](#in-the-field))*

이 목록에는 사용자와 관련된 성능의 다양한 측면을 측정하는 메트릭이 포함되어 있지만 모든 것이 포함되어 있지는 않습니다(예: 런타임 응답성과 원활함은 현재 다루지 않음).

누락된 부분을 다루기 위해 새로운 메트릭이 도입되는 경우도 있지만, 사이트에 맞춤화된 메트릭이 최적일 경우도 있습니다.

## 사용자 지정 메트릭

위에 나열된 성능 메트릭은 웹에 있는 대부분의 사이트의 성능 특성을 전반적으로 이해하는 데 유용합니다. 또한 경쟁 상대와의 성능을 비교하기 위한 일반적인 메트릭으로서의 기능도 합니다.

그러나 고유한 특정 사이트의 경우 전체 성능을 전반적으로 포착하기 위해서 추가 메트릭이 필요한 경우도 있습니다. 예를 들어, LCP 메트릭은 페이지의 기본 콘텐츠 로드가 완료된 시점을 측정하기 위한 것이지만 가장 큰 요소가 페이지의 메인 콘텐츠에 속하지 않는다면 LCP는 이 상황에서 관련성이 없습니다.

이러한 경우를 해결하기 위해 Web Performance Working Group은 자체 사용자 지정 메트릭을 구현하는 데 유용한 하위 수준의 API도 표준화했습니다.

- [User Timing API](https://w3c.github.io/user-timing/)
- [Long Tasks API](https://w3c.github.io/longtasks/)
- [Element Timing API](https://wicg.github.io/element-timing/)
- [Navigation Timing API](https://w3c.github.io/navigation-timing/)
- [Resource Timing API](https://w3c.github.io/resource-timing/)
- [Server timing](https://w3c.github.io/server-timing/)

이러한 API를 사용하여 사이트에 특정한 성능 특성을 측정하는 방법을 알아보려면 [사용자 지정 메트릭](/custom-metrics/)에 대한 가이드를 참조하세요.
