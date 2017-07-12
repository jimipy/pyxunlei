#-*- coding: utf-8 -*-

"""
所有调用的接口具体介绍，参考迅雷云加速开放平台文档
http://open.xunlei.com/wiki/tutorial.html
"""

from ctypes import wintypes, byref, Structure, cdll
from ctypes import c_bool, c_wchar, c_int, c_float, c_longlong, c_uint, POINTER
from threading import Thread

import time

class DownTaskParam(Structure):
    """
    定义 DownTaskParam 结构体
    """
    _pack_ = 1
    _fields_ = [
        ("nReserved1", c_int),
        ("szTaskUrl", c_wchar * 2084),
        ("szRefUrl", c_wchar * 2084),
        ("szCookies", c_wchar * 4096),
        ("szFilename", c_wchar * wintypes.MAX_PATH),
        ("szReserved", c_wchar * wintypes.MAX_PATH),
        ("szSavePath", c_wchar * wintypes.MAX_PATH),
        ("hReserved", wintypes.HWND),
        ("bReserved1", c_bool),
        ("szReserved1", c_wchar * 64),
        ("szReserved2", c_wchar * 64),
        ("IsOnlyOriginal", c_bool),
        ("nReserved2", c_uint),
        ("bReserved2", c_bool),
        ("IsResume", c_bool),
        ("reserved", wintypes.DWORD * 2048)
    ]

"""
stat 参数是 enum 的python 实现
"""
stat = {
    0: lambda: 'NOITEM',
    1: lambda: 'TSC_ERROR',
    2: lambda: 'TSC_PAUSE',
    3: lambda: 'TSC_DOWNLOAD',
    4: lambda: 'TSC_COMPLETE',
    5: lambda: 'TSC_STARTPENDING',
    6: lambda: 'TSC_STOPPENDING',
}


class DownTaskInfo(Structure):
    """
    定义 DownTaskInfo 结构体
    """
    _pack_ = 1
    _fields_ = [
        ('stat', c_int),
        ('fail_code', c_int),
        ('szFilename', c_wchar * wintypes.MAX_PATH),
        ('szReserved0', c_wchar * wintypes.MAX_PATH),
        ('nTotalSize', c_longlong),
        ('nTotalDownload', c_longlong),
        ('fPercent', c_float),
        ('nReserved0', c_int),
        ('nSrcTotal', c_int),
        ('nSrcUsing', c_int),
        ('nReserved1', c_int),
        ('nReserved2', c_int),
        ('nReserved3', c_int),
        ('nReserved4', c_int),
        ('nReserved5', c_longlong),
        ('nDonationP2P', c_longlong),
        ('nReserved6', c_longlong),
        ('nDonationOrgin', c_longlong),
        ('nDonationP2S', c_longlong),
        ('nReserved7', c_longlong),
        ('nReserved8', c_longlong),
        ('nSpeed', c_int),
        ('nSpeedP2S', c_int),
        ('nSpeedP2P', c_int),
        ('bIsOriginUsable', c_bool),
        ('fHashPercent', c_float),
        ('IsCreatingFile', c_int),
        ('reserved', wintypes.DWORD * 64)
    ]

class DownloadEngine:
    """
    云加速sdk接口调用
    """

    def __init__(self):
        """
        加载xldl.dll模块
        """
        self.object_dll = cdll.LoadLibrary('xldl.dll')

    def XL_init(self):
        """
        调用初始化接口XL_init
        """
        self.object_dll.XL_Init()

    def createTask(self, url, filename, savepath):
        """
        调用XL_CreateTask接口，创建下载任务
        """
        down_param = DownTaskParam()
        down_param.szTaskUrl = url
        down_param.szFilename = filename
        down_param.szSavePath = savepath
        down_param.IsResume = True
        self.task_handle = self.object_dll.XL_CreateTask(byref(down_param))

    def startTask(self):
        """
        调用XL_StartTask开始下载
        """
        start_task = self.object_dll.XL_StartTask(self.task_handle)

    def getTaskInfo(self):
        """
        通过创建的任务获得任务执行的返回信息
        """
        task_info = DownTaskInfo()
        self.object_dll.XL_QueryTaskInfoEx.restype = POINTER(DownTaskInfo)
        self.object_dll.XL_QueryTaskInfoEx(self.task_handle, byref(task_info))

        return task_info

    def unInit(self):
        """
        调用XL_UnInit 释放下载资源，退出进程
        """
        self.object_dll.XL_UnInit()

    def stopTask(self):
        """
        暂停下载任务
        """
        task_stat = self.object_dll.XL_StopTask(self.task_handle)
        return task_stat
    
    def deleteTask(self):
        """
        销毁任务
        """
        self.object_dll.XL_DeleteTask(self.task_handle)
        
        
