/*****************************************************
*  
*  Copyright 2009 Adobe Systems Incorporated.  All Rights Reserved.
*  
*****************************************************
*  The contents of this file are subject to the Mozilla Public License
*  Version 1.1 (the "License"); you may not use this file except in
*  compliance with the License. You may obtain a copy of the License at
*  http://www.mozilla.org/MPL/
*   
*  Software distributed under the License is distributed on an "AS IS"
*  basis, WITHOUT WARRANTY OF ANY KIND, either express or implied. See the
*  License for the specific language governing rights and limitations
*  under the License.
*   
*  
*  The Initial Developer of the Original Code is Adobe Systems Incorporated.
*  Portions created by Adobe Systems Incorporated are Copyright (C) 2009 Adobe Systems 
*  Incorporated. All Rights Reserved. 
*  
*****************************************************/
package org.osmf.net.httpstreaming
{
	import flash.events.IEventDispatcher;
	import flash.net.URLRequest;
	
	[ExcludeClass]
	
	/**
	 * @private
	 */
	public class HTTPStreamRequest
	{
		/**
		 * Constructor.
		 *  
		 *  @langversion 3.0
		 *  @playerversion Flash 10
		 *  @playerversion AIR 1.5
		 *  @productversion OSMF 1.0
		 */
		public function HTTPStreamRequest(
			kind:String,
			url:String = null,
			retryAfter:Number = -1,
			bestEffortDownloaderMonitor:IEventDispatcher = null)
		{
			super();
			
			_kind = kind;
			
			if (url)
			{
				_urlRequest = new URLRequest(HTTPStreamingUtils.normalizeURL(url));
			}
			else
			{
				_urlRequest = null;
			}
			
			_retryAfter = retryAfter;
			_bestEffortDownloaderMonitor = bestEffortDownloaderMonitor;
		}
		
		/**
		 * @private
		 * 
		 * The type of event. Must be one of the values in HTTPStreamRequestKind
		 **/ 
		public function get kind():String
		{
			return _kind;
		}
		
		/**
		 * @private
		 * 
		 * Valid when kind is HTTPStreamRequestKind.RETRY or HTTPStreamRequestKind.LIVE_STALL
		 * The amount of time to wait before trying again.
		 * 
		 **/ 
		public function get retryAfter():int
		{
			return _retryAfter;
		}
		
		/**
		 * @private
		 * 
		 * Valid when kind is HTTPStreamRequestKind.BEST_EFFORT_DOWNLOAD
		 * 
		 * HTTPStreamDownloader events will be dispatched to bestEffortDownloaderMonitor instead of the
		 * normal dispatcher. The index handler should be monitored for DOWNLOAD_CONTINUE and DOWNLOAD
		 * SKIP events.
		 * 
		 **/ 
		public function get bestEffortDownloaderMonitor():IEventDispatcher
		{
			return _bestEffortDownloaderMonitor;
		}
		
		/**
		 * @private
		 * 
		 * Valid when kind is HTTPStreamRequestKind.DOWNLOAD or 
		 * HTTPStreamRequestKind.BEST_EFFORT_DOWNLOAD 
		 * 
		 * The urlRequest for the url to download
		 **/ 
		public function get urlRequest():URLRequest
		{
			return _urlRequest;
		}
		
		
		/**
		 * @private
		 * 
		 * Valid when kind is HTTPStreamRequestKind.DOWNLOAD or 
		 * HTTPStreamRequestKind.BEST_EFFORT_DOWNLOAD 
		 * 
		 * The url to download
		 **/
		public function get url():String
		{
			if(_urlRequest == null)
			{
				return null;
			}
			else
			{
				var str:String=_urlRequest.url;
				var proc:String="://";
//				var a:String=str.substring(0,str.indexOf("://")+3);
//				trace(a);
				var hd:String=str.substring(0,str.indexOf(proc)+proc.length);
				var bd:String=str.substring(hd.length);
				bd=bd.replace('//','/');
//				trace(bd.replace('','');+"mmmmmmmm");
//				trace(hd+bd);
				_urlRequest.url=hd+bd;
//				_urlRequest.url="http://221.194.64.16/__5B400k__5D__C8__C8__C0__CB__C7__F2__B0__AE__D5__BD__28__C0__B6__B9__E2__29_0_10.mp4.ts?type=m3u8.web.phone&k=98e999eee29a66568f53ae0fadeb8927-20d0-1369640104";
				return _urlRequest.url;
			}
		}
		
		/**
		 * @private
		 * 
		 * returns a string version of this.
		 **/
		public function toString():String
		{
			var s:String = "[HTTPStreamRequest kind="+kind;
			switch(kind)
			{
				case HTTPStreamRequestKind.BEST_EFFORT_DOWNLOAD:
				case HTTPStreamRequestKind.DOWNLOAD:
					s += ", url="+url;
					break;
				case HTTPStreamRequestKind.LIVE_STALL:
				case HTTPStreamRequestKind.RETRY:
					s += ", retryAfter="+retryAfter;
					break;
				case HTTPStreamRequestKind.DONE:
				default:
					break;
			}
			s += "]";
			return s;
		}
		
		private var _kind:String = null;
		private var _retryAfter:int = -1;
		private var _bestEffortDownloaderMonitor:IEventDispatcher = null;
		private var _urlRequest:URLRequest = null;
	}
}