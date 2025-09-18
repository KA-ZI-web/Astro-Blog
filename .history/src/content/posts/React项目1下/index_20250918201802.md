---
title: 从0到1搭建React+TS项目1（中）
published: 2025-07-15
description: "我的项目解释2"
image: "./cover.jpg"
tags: ["React ","TS","Webpack","craco"]
category: React+TS项目1
draft: false
---
## 三、核心功能实现：路由、组件与交互

### （一）路由配置
:::tip
这里我的思路是先把一个一个页面制作出来，然后在导出页面组件，最终再将页面组件组合起来，用Swiper实现最终效果，所以我在制作时建了一个Login页面，方便我进行页面调试。但是，在最终呈现效果中我并没有显示Login页面。
:::

使用`react-router-dom`实现页面路由功能：

1. 安装路由依赖（需对应TypeScript类型包）
2. 在`App.tsx`中配置路由结构：

```tsx title="App.tsx"
import { BrowserRouter, Routes, Route } from "react-router-dom";
import Login from "./pages/Login";

const App: React.FC = () => {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Login />} />
      </Routes>
    </BrowserRouter>
  );
};
```

3. 实现页面间跳转：在Login页面使用`Link`组件跳转到各个页面

### （二）公共组件开发

以Header组件为例，实现可复用公共组件：

```tsx title="Header.tsx"
import React, { Component } from "react";
import "./Header.css";

class Header extends Component {
  render() {
    return <div className="M-header">Header</div>;
  }
}

export default Header;
```

在Home和Login页面中引入Header组件，保持页面头部风格统一。

### （三）交互功能实现

#### 1. 弹窗组件

基础弹窗组件：

```tsx
import React from "react";
import "./Modal.css";

interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  children: React.ReactNode;
}

const Modal: React.FC<ModalProps> = ({ isOpen, onClose, children }) => {
  if (!isOpen) return null;

  return (
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={(e) => e.stopPropagation()}>
        <button onClick={onClose}>X</button>
        {children}
      </div>
    </div>
  );
};
export default Modal;
```

:::tip
在本项目中需要设置音频播放（浏览器会拦截视频的音乐播放）

那么我就需要两个组件：音频播放组件+视频弹窗播放组件

在现有的弹窗组件里面进行改进：添加监听事件+同步自动播放和状态；处理和音频播放的冲突问题。
:::

```tsx title="Video.tsx"
import { useRef, useState, useEffect } from 'react';
import '../../common/style/video-modal.css';
import { audioManager } from '../Audio/audioManager'; // 导入音频管理器

interface VideoModalProps{
    videoSrc : string;
    isOpen : boolean;
    onClose : () => void;
}

const VideoModal= ({ videoSrc, isOpen, onClose }:VideoModalProps) => {
  const videoRef = useRef(null as HTMLVideoElement | null);
  const [isMuted, setIsMuted] = useState(false);

  // 弹窗打开时静音背景音乐，关闭时恢复
  useEffect(() => {
    if (isOpen) {
      audioManager.mute();
    } else {
      audioManager.unmute();
    }
  }, [isOpen]);

  //监听+同步自动播放和状态
  useEffect (() => {
    const video = videoRef.current;
    if (!video) return;
    const handleVolumeChange = () => setIsMuted(video.muted);
    video.addEventListener('volumechange', handleVolumeChange);
  
    return() => {
      video.removeEventListener('volumechange', handleVolumeChange);
    }
  },[videoSrc]);
  // 处理自动播放逻辑
    useEffect(() => {
        if (!isOpen) return;
        const video = videoRef.current;
        if (!video) return;
        // 当模态框打开时尝试播放
        const attemptPlay = async () => {
            try {
                await video.play();
                setIsMuted(false);
            } catch (err) {
                console.warn('自动播放受限制，需要用户交互后才能播放:', err);
              }
        };
        attemptPlay();
    }, [isOpen, videoSrc]); // 依赖isOpen确保模态框打开时才尝试

  //处理视频焦点和自动播放
  useEffect(()=>{
      if(isOpen && videoRef.current){
          videoRef.current.focus();
      }
  },[isOpen]);

  if (!isOpen) return null;

  return (
    <div className="video-modal-overlay" onClick={onClose}>
      <div className="video-modal-content" onClick={(e) => e.stopPropagation()}>
        <button 
        onClick={() => {
          console.log('关闭按钮被点击');
          onClose();
        }}
        className='close-button'/>
        <video 
            ref={videoRef}
            src={videoSrc}
            muted={isMuted}
            controls
            loop
            playsInline 
            className='video-element'
        />
      </div>
    </div>
  );
};
export default VideoModal;
```
音频组件：这里我多次写打印日志是用来排查音频文件报错的错误。（本项目没有编写测试文件）

```tsx title="Audio.tsx"
class AudioManager {
  private audio: HTMLAudioElement | null = null;
  private isMuted = false;

  // 初始化音频
  init(src: string) {
    this.audio = new Audio(src);
    this.audio.loop = true;
    this.audio.volume = 0.5;
    
    // 尝试自动播放
    this.play().catch((error) => {
      if(error.name === "NotAllowedError"){
      console.log('自动播放被拦截，需要用户交互触发');
    }
    throw error;
  });
  }
  // 播放音频
  async play() {
    if (!this.audio) {
      console.error ("AUDIO_INIT_ERROR: 音频未初始化")
      return;
    }
    try{
      await this.audio.play();
      console.log('AUDIO_PLAY_SUCCESS: 音频播放成功');
    }catch(error:any){
      if (error.name === "NotAllowedError"){
        console.log("AUDIO_AUTOPLAY_BLOCKED: 自动播放被拦截，需用户交互")
      }else{
        console.error(`AUDIO_PLAY_FAILED: ${error.message}`);
      }
      return;
    }
  }
  // 暂停音频
  pause() {
    if (this.audio) {
      this.audio.pause();
    }
  }
  // 静音
  mute() {
    if (this.audio) {
      this.audio.muted = true;
      this.isMuted = true;
    }
  }
  // 取消静音
  unmute() {
    if (this.audio) {
      this.audio.muted = false;
      this.isMuted = false;
    }
  }
  // 切换静音状态
  toggleMute() {
    if (this.isMuted) {
      this.unmute();
    } else {
      this.mute();
    }
  }
}
export const audioManager = new AudioManager();
```

#### 2. 鼠标悬浮交互

利用`useState`钩子管理悬浮状态，实现鼠标跟随效果：
:::tip
调整鼠标灵敏度在CSS中修改'var(--mouse-x)'和'var(--mouse-y)'乘以的系数就可以。例如：

`translateX(calc(var(--mouse-x) * 20px))`

`translateY(calc(var(--mouse-y) * 10px)); ` 
:::
:::note
主要运用了`归一化+视差`的思想，计算鼠标对于中心的相对位置，利用50ms处理一次产生一个视差效果。而归一化的作用主要是使mousePosition.x和mousePosition.y的值始终保持在-1到1之间，使画面等比例变化，将`绝对坐标变为相对坐标`，不会因容器缩放导致效果变形。
:::

```tsx title="src\pages\CharacterCard\index.tsx"
// 存储鼠标相对于容器中心的位置（归一化值）
const [mousePosition, setMousePosition] = useState({ x: 0, y: 0 });
//引用DOM元素
const containerRef = useRef(null);
// 用于存储 CSS 变量的样式对象，可以在CSS中调用
const myStyle: { [key: string]: string | number } = {
  '--mouse-x': mousePosition.x,
  '--mouse-y': mousePosition.y
};
useEffect(() => {
  const container = containerRef.current;
  if (!container) return undefined;
  let lastTime = 0;

  const handleMouseMove = (e: MouseEvent) => {
    // 节流处理：限制 50ms 内只处理一次，优化性能
    const now = Date.now();
    if (now - lastTime < 50) return;
    lastTime = now;

    if (containerRef.current) {
      //getBoundingClientRect()获取一个元素的大小以及其相对于视口的位置
      const rect = containerRef.current.getBoundingClientRect();
      // 计算鼠标在容器内的相对坐标
      const x = e.clientX - rect.left;
      const y = e.clientY - rect.top;
      // 计算容器中心点坐标
      const centerX = rect.width / 2;
      const centerY = rect.height / 2;

      // 归一化处理：将坐标转换为相对于中心的比例（-1 到 1 之间）
      setMousePosition({
        x: (x - centerX) / centerX,
        y: (y - centerY) / centerY
      });
    }
  }; 
    container.addEventListener('mousemove', handleMouseMove);
    return () => container.removeEventListener('mousemove', handleMouseMove);
  }, []);

  useEffect(() => {
  console.log('Position updated:', mousePosition);
  }, [mousePosition]);

```

### （四）页面轮播实现

基于Swiper实现全屏页面轮播，核心要点包括：

1. 设计slide数据结构：每个slide包含标题、文本、图片等完整内容
2. 关键配置参数：
   - `direction: "vertical"`：实现垂直全屏滚动
   - `mousewheel: true`：支持鼠标滚轮控制
   - `keyboard: true`：支持键盘方向键控制
   - `slidesPerView: 1`：每次显示一个slide
3. 性能优化：采用懒加载、分模块加载和虚拟渲染提升大型轮播性能





