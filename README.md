use std::io;

struct Process {
    pid: u32,
    mem_size: u32,
    time_execution: u32,
}

impl Process {
    fn new(pid: u32, mem_size: u32, time_execution: u32) -> Process {
        Process { pid, mem_size, time_execution }
    }
}

struct ProcessStack {
    processes: Vec<Process>,
}

impl ProcessStack {
    fn new() -> ProcessStack {
        ProcessStack { processes: Vec::new() }
    }
    
    fn add_process(&mut self, process: Process) {
        self.processes.push(process);
    }
    
    fn get_next_process(&mut self) -> Option<Process> {
        self.processes.pop()
    }
    
    fn display_summary(&self) {
        println!("Resumo dos processos na pilha:");
        for process in &self.processes {
            println!("PID: {}", process.pid);
        }
    }
}

fn main() {
    let mut process_stack = ProcessStack::new();
    
    println!("Quantos processos serão adicionados?");
    let mut input = String::new();
    io::stdin().read_line(&mut input).expect("Falha ao ler entrada.");
    let num_processes: u32 = input.trim().parse().expect("Entrada inválida.");
    
    for pid in 0..num_processes {
        println!("Processo {}", pid);
        
        println!("Tamanho de memória:");
        let mut input = String::new();
        io::stdin().read_line(&mut input).expect("Falha ao ler entrada.");
        let mem_size: u32 = input.trim().parse().expect("Entrada inválida.");
        
        println!("Tempo de execução (entre 30 e 90 segundos):");
        let mut input = String::new();
        io::stdin().read_line(&mut input).expect("Falha ao ler entrada.");
        let time_execution: u32 = input.trim().parse().expect("Entrada inválida.");
        
        let process = Process::new(pid, mem_size, time_execution);
        process_stack.add_process(process);
    }
    
    process_stack.display_summary();
    
    while let Some(process) = process_stack.get_next_process() {
        println!("Iniciando execução do processo PID: {}", process.pid);
        println!("Processo em execução...");
        for time in 0..process.time_execution {
            println!("Tempo decorrido: {}/{}", time + 1, process.time_execution);
            std::thread::sleep(std::time::Duration::from_secs(1));
        }
        println!("Processo PID {} concluído.", process.pid);
    }
    
    println!("Resumo da execução:");
    process_stack.display_summary();
}
