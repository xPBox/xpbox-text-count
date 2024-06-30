<script lang="ts">
  import { Runtime } from '@xpbox/sdk'
  import { onMount } from 'svelte'

  const runtime = new Runtime()

  let text = ''

  onMount(() => {
    runtime.app.ready()
  })

  const onClear = () => {
    text = ""
  }

  let sTotal = 0; //双字节字符;
  let iTotal = 0; //中文字符；
  let eTotal = 0; //英文字符
  let total = 0;
  let iNum = 0;

  $: {
    let Words = text;
    let W = {};
    sTotal = 0;
    iTotal = 0;
    eTotal = 0;
    total = 0;
    iNum = 0;

    let iNumWords = 0;
    let sNumWords = 0;
    let otherTotal = 0;
    let bTotal = 0;

    for (let i = 0; i < Words.length; i++) {
      let c = Words.charAt(i);
      if (c.match(/[\u4e00-\u9fa5]/)) {
        if (isNaN(W[c])) {
          iNumWords++;
          W[c] = 1;
        }
        iTotal++;
      }
    }

    for (let i = 0; i < Words.length; i++) {
      let c = Words.charAt(i);
      if (c.match(/[^\x00-\xff]/)) {
        if (isNaN(W[c])) {
          sNumWords++;
        }
        sTotal++;
      }
      else {
        eTotal++;
      }
      if (c.match(/[0-9]/)) {
        iNum++;
      }
    }
    total = iTotal * 2 + (sTotal - iTotal) * 2 + eTotal;
  }
</script>

<div class="main">
<!--  <div class="title">字符数统计</div>-->
  <div class="editor-wrap">
    <textarea class="editor" bind:value={text} />
    <div class="footer">
      <div style="flex: 1">
        <div>
          <span class="item">中文</span>
          <span class="value">{iTotal}</span>
          <span class="item">英文</span>
          <span class="value">{eTotal}</span>
        </div>
        <div>
          <span class="item">字符数</span>
          <span class="value">{total}</span>
          <span class="item">数字</span>
          <span class="value">{iNum}</span>
        </div>
      </div>
      <div class="btn" on:click={onClear}>清空</div>
    </div>
  </div>
</div>

<style lang="scss">
  .title {
    color: #333;
    margin-bottom: 12px;
    font-size: 18px;
  }
  .main {
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    overflow: hidden;
    box-sizing: border-box;
    padding: 8px;
    background: #fff;
  }

  .editor {
    outline: none;
    margin-bottom: 12px;
    color: #333;
    resize: none;
    width: 100%;
    height: 100%;
    border: none;
    padding: 12px 12px 56px;
  }

  .editor-wrap {
    flex: 1;
    width: 100%;
    position: relative;
    border: 1px solid #ccc;
    border-radius: 12px;
    overflow: hidden;
  }

  .footer {
    position: absolute;
    bottom: 0;
    left: 0;
    background: #eeeeeeaa;
    align-items: center;
    display: flex;
    width: 100%;
    padding: 6px 12px;
    font-size: 12px;
    box-sizing: border-box;
    backdrop-filter: blur(4px);
    border-radius: 0 0 12px 12px;

    .value {
      margin-left: 4px;
      font-size: 14px;
      color: #0080ee;
      margin-right: 8px;
    }

    .btn {
      border-radius: 4px;
      background-color: #fff;
      padding: 4px 8px;
      user-select: none;
      cursor: pointer;
      transition: background-color 0.3s;

      &:hover {
        background-color: #eee;
      }
    }
  }
</style>
