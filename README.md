# winit-async

Experimental asynchronous event loop for Winit.

## Usage
Copied from the example:

```rust
#![feature(async_await, async_closure)]
use winit::{
    event::{WindowEvent},
    event_loop::EventLoop,
    window::WindowBuilder,
};
use winit_async::{EventLoopAsync, EventAsync as Event};

fn main() {
    let event_loop = EventLoop::new();
    let window = WindowBuilder::new().build(&event_loop).unwrap();

    event_loop.run_async(async move |mut runner| {
        'main: loop {
            runner.wait().await;

            let mut recv_events = runner.recv_events().await;
            while let Some(event) = recv_events.next().await {
                match event {
                    Event::WindowEvent {
                        event: WindowEvent::CloseRequested,
                        ..
                    } => {
                        break 'main;
                    },
                    _ => println!("{:?}", event),
                }
            }

            window.request_redraw();

            let mut redraw_requests = recv_events.redraw_requests().await;
            while let Some(window_id) = redraw_requests.next().await {
                println!("redraw {:?}", window_id);
            }
            println!();
        }
    })
}
```
